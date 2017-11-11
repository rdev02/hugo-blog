---
title: String.split() VS String.startsWith() VS Regex simple performance comparison.
author: Viktor Halitsyn
date: 2011-04-19T14:45:00+00:00
type: post
categories:
  - tech
  - code
tags:
  - java
  - perf
  - csharp

---
Now really, what would be faster/more reliable for a typical not so complicated and not over-simple case? Yes the one we tend to deal with in our day-to-day work? For those impatient [benchmarks are here][1].Reading a 100/1000-line file is not fun and I really think you shouldn&#8217;t bother choosing if you&#8217;re dealing with such volumes of data. But if you have to parse 320k-500k records lines, which, in fact, is not that large &#8211; 320k of CSV data is ~ 12MB performance is considerable. So let&#8217;s start with data definition. Let&#8217;s assume we have data like 

> 1812332,&#8221;String1&#8243;,&#8221;String2&#8243;,&#8221;String3&#8243;,16, 
  
> 1812332,&#8221;String1&#8243;,&#8221;String2&#8243;,&#8221;String3&#8243;,16, 
  
> 1812332,&#8221;String1&#8243;,&#8221;String2, extra string&#8221;,&#8221;String3&#8243;,16, 

Also we are going to start with the abstract &#8220;blank&#8221; for csv reading like:
  


```cs
public abstract class CsvStreamReader implements Iterator<CsvData> {
    protected BufferedReader reader;

    /**
     * creates an new {@code CsvStreamReader}. Caller is responsible for stream management.
     *
     */
    public CsvStreamReader() {
    }

    public abstract CsvData next();

    public boolean hasNext() {
        try {
            return reader.ready();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return false;
    }

    @Deprecated
    public void remove() {
        // not used
    }

    public void setInputStream(InputStream inputStream){
        reader = new BufferedReader(new InputStreamReader(inputStream));
    }
}
```

And a simple data-holder like:
  

```cs
public class CsvData {
    private String[] data;

    public CsvData(String... data) {
        this.data = new String[data.length];
        System.arraycopy(data, 0, this.data, 0, data.length);
    }

    public String getColumn(int index) {
        return data[index];
    }

    public int getColumnCount() {
        return data.length;
    }

    @Override
    public String toString() {
        return "CsvData{" +
          "data=" + (data == null ? null : Arrays.asList(data)) +
          '}';
    }
}
```

Now the first and obvious way to read and parse the CSV would be smth like:
  


```cs
public class SplitBasedReader extends CsvStreamReader {

    public CsvData next() {
        try {
            String line = reader.readLine();
            return new CsvData(line.split(","));
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

This looks fairly good with one lil&#8217; problem: it doesn&#8217;t work good. If we take the last line with &#8220;String2, extra string&#8221; we see that the split would play a bad trick with us breaking the data consistency.
  
A quick fix would suggest smth like:
  

```cs
public class StringBasedReader extends CsvStreamReader {
    private static final String QUOTE = """;

    @Override
    public CsvData next() {
        try {
            List<String> data = new ArrayList<String>();
            final String line = reader.readLine();
            int commaStart = 0;
            int commaEnd = 0;
            StringBuffer possibleToken = new StringBuffer();

            while (commaEnd >= 0) {
                commaEnd = line.indexOf(',', commaStart + 1);
                if (commaEnd < 0) {
                    break;
                }
                final String possibleColumn = line.substring(commaStart, commaEnd);
                possibleToken.append(possibleColumn);
                commaStart = commaEnd + 1;

                final int length = possibleToken.length();

                boolean columnIsOpen = (possibleColumn.startsWith(QUOTE)) && (!(possibleColumn.endsWith(QUOTE)));
                if (!columnIsOpen) {
                    data.add(possibleToken.toString());
                    possibleToken.delete(0, length);
                }
            }

            if (possibleToken.length() != 0) {
                data.add(possibleToken.toString());
            }

            return new CsvData(data.toArray(new String[]{}));
        } catch (IOException ioex) {
            ioex.printStackTrace();
        }
        return null;
    }
}
```

Well yeah, but having string.startsWith checking on String all this takes int account variable length which in fact we do not care about&#8230; sounds wasteful. Let&#8217;s improve it.
  

```cs
public class StringBasedReaderImproved extends CsvStreamReader {
    private static final char QUOTE = '"';

    @Override
    public CsvData next() {
        try {
            List<String> data = new ArrayList<String>();
            final String line = reader.readLine();
            int commaStart = 0;
            int commaEnd = 0;
            StringBuffer possibleToken = new StringBuffer();

            while (commaEnd >= 0) {
                commaEnd = line.indexOf(',', commaStart + 1);
                if (commaEnd < 0) {
                    break;
                }
                possibleToken.append(line.substring(commaStart, commaEnd));
                commaStart = commaEnd + 1;

                final int length = possibleToken.length();
                boolean columnIsOpen = (possibleToken.charAt(0) == QUOTE) && (!(possibleToken.charAt(length - 1) == QUOTE));
                if (!columnIsOpen) {
                    data.add(possibleToken.toString());
                    possibleToken.delete(0, length);
                }
            }

            if (possibleToken.length() != 0) {
                data.add(possibleToken.toString());
            }

            return new CsvData(data.toArray(new String[]{}));
        } catch (IOException ioex) {
            ioex.printStackTrace();
        }
        return null;
    }
}
```

OKAY! now it a char and `StringBuffer`. Looks like we&#8217;ve done a great job! Awesome&#8230;. although a bit magical. It&#8217;s not rocket science but still it&#8217;s not pleasant to read. What else might we suggest? Regex.
  


```cs
public class RegexBasedReader extends CsvStreamReader {
    private static final String RE_ANY_INSIDE_QUOTES = ""([^"]*)"";
    private static final String RE_ANY_WITHOUT_COMMAS = "([^,]*)";
    private static final String RE_ANY = "(" + RE_ANY_INSIDE_QUOTES + "|" + RE_ANY_WITHOUT_COMMAS + ")";
    private static final String RE_6_DIGIT_GROUP = "(\d{6})";

    private static final String CSV_REGEX = "^" + RE_6_DIGIT_GROUP + "," + RE_ANY + "," + RE_ANY + "," + RE_ANY + "," + RE_ANY + ",$";


    protected final Pattern pattern;

    /**
     * {@inheritDoc}
     */
    public RegexBasedReader() {
        pattern = Pattern.compile(CSV_REGEX);
    }

    public CsvData next() {
        try {
            List<String> data = new ArrayList<String>();
            final String line = reader.readLine();
            final Matcher matcher = pattern.matcher(line);
            matchDataToColumns(data, matcher);

            return new CsvData(data.toArray(new String[]{}));
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * @param data
     * @param matcher
     */
    protected void matchDataToColumns(List<String> data, Matcher matcher) {
        if (matcher.matches()) {
            final int groupCount = matcher.groupCount();
            assert groupCount == 13 : "Matched an invalid string - regex should be revised";

            final String data1 = matcher.group(1);
            assert data1 != null : "data1 should always be present";
            data.add(data1);

            String data2 = matcher.group(3);
            if (data2 == null) {
                data2 = matcher.group(4);
            }
            assert data2 != null : "data2 should somehow be present";
            data.add(data2);

            String data3 = matcher.group(6);
            if (data3 == null) {
                data3 = matcher.group(7);
            }
            assert data3 != null : "data3 should somehow be present";
            data.add(data3);

            String data4 = matcher.group(9);
            if (data4 == null) {
                data4 = matcher.group(10);
            }
            assert data4 != null : "data4 should somehow be present";
            data.add(data4);

            String data5 = matcher.group(12);
            if (data5 == null) {
                data5 = matcher.group(13);
            }
            assert data5 != null : "data5 should somehow be present";
            data.add(data5);
        }
    }
}
```

That looks like Chuck Norris would be proud. Strong and reliable checking, precompiled patern, groups of data, many cases handled same way&#8230; room for extension etc.

* * *Now lets look at the 

**benchmarks** reading over **500k** lines:
  
</p> 

```
public class Test2 {
    static List<string> times = new ArrayList<string>(4);

    public static void main(String[] args) throws IOException {

        read320KRecords(new SplitBasedReader());
        read320KRecords(new StringBasedReader());
        read320KRecords(new StringBasedReaderImproved());
        read320KRecords(new RegexBasedReader());
}

    private static void read320KRecords(CsvStreamReader reader) throws IOException {
        InputStream inputStream = Test2.class.getResourceAsStream("/CardInfo.csv");
        reader.setInputStream(inputStream);
        long timeStart = System.currentTimeMillis();
        while (reader.hasNext()) {
            CsvData csvData = reader.next();
        }
        long timeEnd = System.currentTimeMillis();
        times.add(reader.toString() + "  time: " + (new DecimalFormat("#.####")).format(new Double(
          (timeEnd - timeStart) / 1000.0)) + " sec.");
        inputStream.close();
    }
}
```

<a href="http://www.blogger.com/post-create.g?blogID=4242186654644920321" name="benchmarks">Output <i>run from IntellyJ Idea</i>:</a>

<table border="1" cellpadding="0" cellspacing="0">
  <tr>
    <td>
       
    </td>
    
    <td>
      <b>SplitBasedReader</b>
    </td>
    
    <td>
      <b>StringBasedReader</b>
    </td>
    
    <td>
      <b>StringBasedReaderImproved</b>
    </td>
    
    <td>
      <b>RegexBasedReader</b>
    </td>
  </tr>
  
  <tr>
    <td>
      Run 1
    </td>
    
    <td>
      1.468
    </td>
    
    <td>
      0.968
    </td>
    
    <td>
      0.984
    </td>
    
    <td>
      1.593
    </td>
  </tr>
  
  <tr>
    <td>
      Run 2
    </td>
    
    <td>
      1.562
    </td>
    
    <td>
      0.953
    </td>
    
    <td>
      0.985
    </td>
    
    <td>
      1.594
    </td>
  </tr>
  
  <tr>
    <td>
      Run 3
    </td>
    
    <td>
      1.531
    </td>
    
    <td>
      1.031
    </td>
    
    <td>
      1.047
    </td>
    
    <td>
      1.625
    </td>
  </tr>
</table>

In a chart:

{{<image classes="fancybox left" src="https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2011/04/csv_comparizon.jpg">}}

_<u>But wait &#8211; there&#8217;s more 🙂</u>_
  
Now let&#8217;s see if we can get even more results out of this. In the previous benchmark runs we did nothing with the CsvData we obtained, but that is not the case in real world right? now consider we add a single
  


<pre>System.out.println("csvData = " + csvData);</pre>

and this would always result in &#8220;csvData = myCSvData&#8221;.
  
Running this in IDEA gives next results:

SplitBasedReader time: 14.634 sec.
  
StringBasedReader time: 13.086 sec
  
StringBasedReaderImproved time: 12.118 sec
  
RegexBasedReader time: 15.244 sec

**SAME but in console: **

SplitBasedReader time: 138.025 sec.
  
StringBasedReader time: 136.337 sec.
  
StringBasedReaderImproved time: 130.975 sec.
  
RegexBasedReader time: 131.146 sec.

So we see that the buffers and IO impact is immense.
  
And one more test. Lets assume out toString is smth. a little meaningful like
  


> <pre>"CsvData{" + data[0] + data[1] + data[2];
</pre>

Now the times(idea env execution) are:

SplitBasedReader time: 21.655 sec.
  
StringBasedReader time: 9.772 sec.
  
StringBasedReaderImproved time: 9.256 sec.
  
RegexBasedReader time: 12.461 sec.

* * *

**Bottom line:**</p> 

  * While robust and effective Regex is not the fastest thing in the world;
  * working with relatively not-so-complex data is faster with &#8220;smaller&#8221; API&#8217;s;
  * ;
  
    IO is a bottleneck, not the parsing, usually.

 [1]: https://blog.vnomad.com/#benchmarks