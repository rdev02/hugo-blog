---
title: 'Nokia OVI suite & sms extract.'
author: Viktor Halitsyn
date: 2010-12-27T00:00:00+00:00
type: post
categories:
  - tech
tags:
  - hack

---
There was a nice program for syncing phone and PC called Nokia PC Suite. While still available on some dusty hard drives it is now replaced with something called Nokia Ovi Suite. As I [mentioned][1] before &#8211; nice piece of software if we forget about all the &#8220;store&#8221; thing. But it has one important for me thing missing &#8211; sms extracting mechanism. You can&#8217;t just take all of your sms and store to the file you want. It will either go into backup file or will be stored on you PC. I was really disappointed to learn that so I tried to find where exactly is that info stored on my PC. First search revealed that they use the SQLite db for storing all data located in 
  
&#8220;c:Users[username]AppDataLocalNokiaNokia Data StoreDataBaseMDataStore.db3&#8221; 
  
The DB is not in any way protected or encrypted so I felt free to dig into my own data. Downloaded a splendid [SQLiteManager][2] plugin and started seeking my sms&#8217;s. Didn&#8217;t take long to find. Whereas the DB structure might be a subject of argue I&#8217;ll post some code I used to get the info I needed out of the DB:

```cs
class Program
    {
        static void Main(string[] args)
        {
            if (args == null || args.Length < 1)
            {
                Console.Out.WriteLine("Please specify db3 file path as an argument");
                return;
            }
            string baseName = args[0];
            StreamWriter writer = null;
            try
            {
                SQLiteFactory factory = (SQLiteFactory)DbProviderFactories.GetFactory("System.Data.SQLite");
                using (SQLiteConnection connection = (SQLiteConnection)factory.CreateConnection())
                {
                    connection.ConnectionString = "Data Source = " + baseName;
                    connection.Open();
                    Console.WriteLine("Connection opened..");
                    using (SQLiteCommand command = new SQLiteCommand(connection))
                    {
                        command.CommandText = @"SELECT MessageEx.Sender, MessageEx.Text, MessageEx.SentReceivedTimestamp, Item.CreationDate FROM MessageEx INNER JOIN Item ON MessageEx.GUID = Item.GUID ORDER BY MessageEx.SentReceivedTimestamp;";
                        var reader = command.ExecuteReader();
                        FileInfo outF = new FileInfo("result" + DateTime.Now.Date.ToString("dd_mm_yyyy") + ".txt");
                        writer = new StreamWriter(outF.Open(FileMode.Create));
                        writer.WriteLine("Sender, Text, Timestamp, Created");
                        while (reader.Read())
                        {
                            writer.WriteLine(String.Format("{0}, {1}, {2}, {3}", reader.GetString(0), reader.GetString(1), reader.GetDouble(2), reader.GetDouble(3)));
                            Console.Write(".");
                        }
                        Console.WriteLine("Done! Press eny Key to continue.");
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("OOPS:" + ex.Message);
            }
            finally {
                if (writer != null)
                    writer.Close();
            }

        }
    }
```

This is pretty much all needed to get a well-formed CSV. There is still some work left to get the date from Real to date/time, but I thought it can wait until I really need that info. CSV is easily processed:). Some of this sourcecode is borrowed from [here][3]. Personally wouldn&#8217;t ever dig into such software peripherals, but they literally forced me to, not providing the info I actually poses.
  
Hope that helps someone who needs data synchronized from the phone &#8211; the DB is a pretty much comprehensible structure and tables and with the mentioned above plugin and a lil&#8217; SQL it&#8217;s quite easy to get your data even without programming.

 [1]: http://revengebloggings.blogspot.com/2010/02/nokia-ovi-suite.html
 [2]: https://addons.mozilla.org/en-US/firefox/addon/5817/
 [3]: http://habrahabr.ru/blogs/net/56694/