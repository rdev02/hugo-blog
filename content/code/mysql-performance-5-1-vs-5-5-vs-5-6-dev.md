---
title: MySql performance. 5.1 VS 5.5 VS 5.6 (dev)
author: Viktor Halitsyn
date: 2011-09-14T06:50:00+00:00
excerpt: 'Recently had to do an investigation about MySql server performance. So what I basically did was: created a winXP wmWare image, created a script for generating populating sql; installed db server version on that machine and kept track of times while executing sql statements; reverted to the original virgin machine state; start again.'
type: post
categories:
  - tech
disqusIdentifier: 5154602234
tags:
  - code
  - mysql

---
Recently had to do an investigation about MySql server performance. So what I basically did was: created a winXP wmWare image, created a script for generating populating sql; installed db server version on that machine and kept track of times while executing sql statements; reverted to the original virgin machine state; start again. The versions I tested on were MySql community server 5.1, 5.5 and the development verion of 5.6. Here is the chart that represents my findings[the smaller the better]:

{{<image classes="fancybox left" src="https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2011/09/msqlChart.jpg">}}

As a quick summary &#8211; interestingly enough I do not see any speed improvement since 5.1:) there is one between 5.5 and 5.6 but it&#8217;s nowhere near the 5.1.
  
Now just a quick overview of what I used to actually get this chart.
  
The testTable:
  
The queries:

<table border="1">
  <tr>
    <td>
      Q1
    </td>
    
    <td>
      select count(*) from test.testtable;
    </td>
  </tr>
  
  <tr>
    <td>
      Q2
    </td>
    
    <td>
      select count(*) from testtable;
    </td>
  </tr>
  
  <tr>
    <td>
      Q3
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.id>1000 AND tst.id<10000;
    </td>
  </tr>
  
  <tr>
    <td>
      Q4
    </td>
    
    <td>
      SELECT COUNT(*) FROM testtable tst WHERE tst.id>1000 AND tst.id<10000;
    </td>
  </tr>
  
  <tr>
    <td>
      Q5
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.testInt between 10000 AND 100000 ;
    </td>
  </tr>
  
  <tr>
    <td>
      Q6
    </td>
    
    <td>
      SELECT COUNT(*) FROM testtable tst WHERE tst.testInt between 10000 AND 100000 ;
    </td>
  </tr>
  
  <tr>
    <td>
      Q7
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.testDouble between 0.1 AND 0.5 ;
    </td>
  </tr>
  
  <tr>
    <td>
      Q8
    </td>
    
    <td>
      SELECT COUNT(*) FROM testtable tst WHERE tst.testDouble between 0.1 AND 0.5 ;
    </td>
  </tr>
  
  <tr>
    <td>
      Q9
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.testDouble <= 0.1 AND tst.testDouble > 0.5 ;
    </td>
  </tr>
  
  <tr>
    <td>
      Q10
    </td>
    
    <td>
      SELECT COUNT(*) FROM testtable tst WHERE tst.testDouble >= 0.1 AND tst.testDouble < 0.5 ;
    </td>
  </tr>
  
  <tr>
    <td>
      Q11
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.testDate > &#8216;2005/08/04 07:00&#8217;;
    </td>
  </tr>
  
  <tr>
    <td>
      Q12
    </td>
    
    <td>
      SELECT COUNT(*) FROM testtable tst WHERE tst.testDate > &#8216;2005/08/04 07:00&#8217;;
    </td>
  </tr>
  
  <tr>
    <td>
      Q13
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.testVarchar LIKE &#8216;%AAAAAA&#8217;;
    </td>
  </tr>
  
  <tr>
    <td>
      Q14
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.testVarchar LIKE &#8216;%AAAAAA&#8217; OR tst.testVarchar LIKE &#8216;BBBBBB%&#8217;;
    </td>
  </tr>
  
  <tr>
    <td>
      Q15
    </td>
    
    <td>
      SELECT * FROM testtable tst WHERE tst.testInt between 10000 AND 100000 AND tst.testDouble between 0.1 AND 0.5 AND tst.testDate > &#8216;2005/08/04 07:00&#8217; OR tst.id> 9000000
    </td>
  </tr>
  
  <tr>
    <td>
      Q16
    </td>
    
    <td>
      SELECT COUNT(*) FROM testtable tst WHERE tst.testInt between 10000 AND 100000 AND tst.testDouble between 0.1 AND 0.5 AND tst.testDate > &#8216;2005/08/04 07:00&#8217; OR tst.id> 9000000
    </td>
  </tr>
</table>

Absolute times and machine spec can be found [here][1].
  
Of course this is the developer machine setup and ran on one virtual machine, but still I thought that would be interesting for other to know. Ireally looking forward to making some more production-like tests now&#8230;
  
One more thing in case interested &#8211; the code snippet I used for generating the data:

```cs
public class CreateTestTableSql {

    private static final int QUERY_ROWS = 10 * 1000 * 1000;
    private static final int BATCH_SIZE = 10 * 1000;
    private static final boolean USE_BATCH = true;

    static Random r = new Random();
    static SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/dd HH:mm");

    public static void main(String[] args) throws Exception {

        StringBuilder spotTradeSQLRow = new StringBuilder();


        File f = new File(USE_BATCH ? "batchmysql.sql" : "mysql.sql");
        if (f.exists()) {
            f.delete();
        }

        assert f.createNewFile();

        BufferedWriter out = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(f)));

        System.out.println("Start");
        int batchCounter = 0;
        for (int i = 0; i < QUERY_ROWS; i++) {
            if (USE_BATCH) {
                if (batchCounter == BATCH_SIZE) {
                    spotTradeSQLRow.append(";n");
                    batchCounter = 0;
                }
                if (batchCounter == 0) {
                    spotTradeSQLRow.append("INSERT INTO test.testtable VALUES(");
                } else {
                    spotTradeSQLRow.append(",(");
                }
            } else {
                spotTradeSQLRow.append("INSERT INTO test.testtable VALUES(");
            }
            spotTradeSQLRow.append(i + ","); //id
            spotTradeSQLRow.append((r.nextBoolean() ?( "'" +getRandomString() + "'"): "NULL") + ","); //testVarchar
            spotTradeSQLRow.append((r.nextBoolean() ? r.nextInt() : "NULL") + ","); //testInt
            spotTradeSQLRow.append((r.nextBoolean() ? r.nextDouble() : "NULL") + ","); //testInt
            spotTradeSQLRow.append((r.nextBoolean() ? ("'" + getRandomDate() + "'") : "NULL") +
              ")"); //testInt
            if (!USE_BATCH) {
                spotTradeSQLRow.append(";");
            }
            out.write(spotTradeSQLRow.toString());
            out.write("n");
            spotTradeSQLRow.delete(0, spotTradeSQLRow.length());
            batchCounter++;

        }
        if (USE_BATCH) {
           out.write(";");
        }

        out.close();
        System.out.println("end");

    }

    private static String getRandomDate() {
        int rand = r.nextInt(10);
        rand *= (r.nextBoolean() ? -1 : 1);
        final Calendar cal = Calendar.getInstance();
        cal.add(Calendar.YEAR, rand);
        cal.add(Calendar.DAY_OF_YEAR, rand);
        cal.add(Calendar.HOUR_OF_DAY, rand);

        return sdf.format(cal.getTime());

    }

    private static String getRandomString() {
        final int rand = r.nextInt(10);
        if (rand < 3) {
            return "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA";
        } else if (rand >= 3 && rand < 6) {
            return "BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB";
        } else {
            return "CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC";
        }
    }
}

```

 [1]: https://docs.google.com/spreadsheet/ccc?key=0AtItSpPjhRWvdDlDbklHUzZxNFBFNkRya3RNbng5ZWc&hl=en_US