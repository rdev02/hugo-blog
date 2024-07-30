---
title: Trying out Google base
author: Viktor Halitsyn
date: 2008-06-10T09:44:00+00:00
categories:
  - tech
  - code
tags:
  - java

---
Subj is only at beta but is really nice! Though not very complicated and lots of info on google sites i found it still quite time consuming to write a single posting app&#8230; But when finished everything looked very nice! 

```java
class Program
    {
        const string GOOGLE_LOGIN_SECTION = "googleLogin";
        const string BATCH_COUNT_SETTING = "batchCount";
        const string CHECK_EXISTANCE_SETTING = "checkExistance";
        const string MyItem_ITEMTYPE_SETTING = "MyItemItemType";
        const string MyDB_CONNECTION_STRING = "MyDBConnectionString";

        static void Main(string[] args)
        {
            string appNameG = Utils.AssemblyCompany + "-" + Utils.AssemblyTitle + "-" + Utils.AssemblyVersion;
            string userName, passs;
            int batchCount;
            bool checkExistance = false;
            SqlConnection connection;

            // configuration parsing
            Console.Write("Initializing...");
            try
            {
                GoogleLoginSection gLogin = ConfigurationManager.GetSection(GOOGLE_LOGIN_SECTION) as GoogleLoginSection;
                userName = gLogin.UserName;
                passs = gLogin.Password;
                batchCount = int.Parse(ConfigurationManager.AppSettings[BATCH_COUNT_SETTING]);
                MyItem.MyItem_TYPE = ConfigurationManager.AppSettings[MyItem_ITEMTYPE_SETTING];
                checkExistance = bool.Parse(ConfigurationManager.AppSettings[CHECK_EXISTANCE_SETTING]);

                connection = new SqlConnection(ConfigurationManager.ConnectionStrings[MyDB_CONNECTION_STRING].ConnectionString);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Failed configuration parsing. {0}", ex.Message);
                return;
            }
            Console.WriteLine(" OK.");

            //get a google service
            Console.Write("Logging to Google....");
            GBaseService service = null;
            try
            {
                GDataGAuthRequestFactory fact = new GDataGAuthRequestFactory("xapi", appNameG);
                GDataCredentials myCred = new GDataCredentials(userName, passs);
                string developerToken = fact.QueryAuthToken(myCred);
                service = new GBaseService(appNameG, developerToken);
                service.Credentials = myCred;
            }
            catch (Exception ex)
            {
                Console.WriteLine("Google authorization failed! {0}", ex.Message);
                return;
            }
            Console.WriteLine(" OK.");

            Console.Write("Connecting to database....");
            //acquire reader and send data in batches
            SqlDataReader reader = Utils.GetReader(connection);
            if (reader == null) return;
            Console.WriteLine(" OK.");
   //reader.Read();
   //service.Insert(GBaseUriFactory.Default.ItemsFeedUri, (new MyItem(reader)).CreateGEntry(new AtomPerson(AtomPersonType.Author, userName), 1));

            GBaseQuery query = new GBaseQuery(GBaseUriFactory.Default.ItemsFeedUri);
            MyItem workMyItem;
            GBaseFeed resultQ = null;
            AtomFeed batchFeed;
            AtomPerson author = new AtomPerson(AtomPersonType.Author, userName);

            batchFeed = GetNewBatchFeed(service);
            int processed = 1;
            Console.WriteLine("Processing data in batches of {0}", batchCount);
            while (reader.Read())
            {
                workMyItem = new MyItem(reader);
                Console.Write("{0,-45}", workMyItem);
                if (workMyItem.Name.Length < 3) { Console.WriteLine("MyItem has too short name to be stored GBase!"); continue; }
                if (checkExistance)
                {
                    try
                    {
                        query.GoogleBaseQuery = string.Format("[item type: {0}] ("{1}")", MyItem.MyItem_TYPE, workMyItem.Name.Replace(':', ' '));//search replacing COLONS a these are service charackters.
                        resultQ = service.Query(query);
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("!!!Searching failed...!!!n{0}", ex.Message);
                    }
                }

                if (checkExistance && (resultQ.Entries.Count > 0))
                {
                    Console.WriteLine(" -Found {1} matching item(s). Skipping.");
                }
                else
                {
                    batchFeed.Entries.Add(workMyItem.CreateGEntry(author, processed));
                    Console.WriteLine(" Added to batch.");
                    processed++;
                }
                if (processed == batchCount)
                {
                    Console.WriteLine("n**Sending batch...");
                    if (!SendBatch(service, batchFeed)) return;
                    batchFeed = GetNewBatchFeed(service);
     processed = 1; 
                }
            }
            if (processed > 1) { SendBatch(service, batchFeed); }
   
   //clean up
   try
   {
    connection.Close();
    reader.Close();
   }
   catch (Exception ex)
   {
    Console.WriteLine("Could not release connection resources.{0}", ex.Message);    
   }

        }

        private static AtomFeed GetNewBatchFeed(GBaseService service)
        {
            AtomFeed batchFeed;
            batchFeed = new AtomFeed(GBaseUriFactory.Default.ItemsFeedBatchUri, service);
            batchFeed.BatchData = new GDataBatchFeedData();
            return batchFeed;
        }

        private static bool SendBatch(GBaseService service, AtomFeed batchFeed)
        {            
            try
            {
                AtomFeed result = service.Batch(batchFeed, GBaseUriFactory.Default.ItemsFeedBatchUri);
                Console.WriteLine("nn*** {0} entries published.", result.Entries.Count);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Couldn't publish batch data. {0}nContinue?(y/n)", ex.Message);
                ConsoleKeyInfo key = Console.ReadKey();
                if (key.KeyChar == 'y') { return false; }
            }
            return true;
        }
    }
```