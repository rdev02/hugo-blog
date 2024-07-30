---
title: 'Switching to  container based datasource…'
author: Viktor Halitsyn
date: 2010-01-11T12:08:00+00:00
categories:
  - tech
  - code
tags:
  - java
  - hibernate

---
    Quite a time ago had to do this but had no time for posting a complete solution. Read a few articles on that so that I got the idea. Although it looks quite simple you might run into several configuration issues firing weird and long stacktraces. To get rid of those I&#8217;ll describe the way it worked for me with some additional comments.
  
    First of all lets assume we use JBoss app server. Different versions of JBoss have their own configuration &#8220;specials&#8221; so I&#8217;ll stick to 5.0.1 jdk 6. First of all we need to register the desired datasource withing the container obtaining a JNDI name. Example configurtion files for registration can be found in [JBoss install dir]/docsexamplesjca. The resulting config file which has to be pout under server/[configuration_name]/deploy would look like

```xml
<local-tx-datasource>
    <jndi-name>MySqlDS</jndi-name>
    <connection-url>jdbc:mysql://localhost:3306/mydatasource</connection-url>
    <driver-class>com.mysql.jdbc.Driver</driver-class>
    <user-name>user</user-name>
    <password>pwd</password>
    <exception-sorter-class-name>org.jboss.resource.adapter.jdbc.vendor.MySQLExceptionSorter</exception-sorter-class-name>
    <metadata>
       <type-mapping>mySQL</type-mapping>
    </metadata>
 </local-tx-datasource>
```

Then in hibernate properties
  

```
###### JNDI std JBOSS connection ####################
hibernate.connection.datasource = java:MySqlDS  
hibernate.transaction.factory_class = org.hibernate.transaction.JTATransactionFactory
hibernate.transaction.manager\_lookup\_class = org.hibernate.transaction.JBossTransactionManagerLookup
```
Deploying your app, especially if you deployed with Tomcat compatibility you might see the next exception on startup:
  
```
ERROR [org.apache.catalina.core.ContainerBase.[jboss.web].[localhost].[/mydb]] (main) Exception starting filter ServiceFilter
java.lang.<b>ClassCastException</b>: com.arjuna.ats.jbossatx.jta.TransactionManagerDelegate cannot be cast to javax.transaction.TransactionManager
at org.hibernate.transaction.JNDITransactionManagerLookup.getTransactionManager(JNDITransactionManagerLookup.java:23)
at org.hibernate.impl.SessionFactoryImpl.SessionFactoryImpl.java:264)
at org.hibernate.cfg.Configuration.buildSessionFactory(Configuration.java:1055)
```
In short this means that you have a classloader issue having loaded the wrong manager first. Most likely you have the javax-transaction.jar in your bin. Remove it and everything will work fine :).


If you need to set up the very same thing on Tomcat, then you have to keep in mind the next thing
  
> Tomcat is a hybrid sort of environment (neither fish nor fowl) from the standpoint of Hibernate. Tomcat provides a JNDI and DataSource, unlike a standalone application. The DataSource facility should facilitate portability among application servers. But like a standalone environment, Tomcat provides no Transaction Manager. Hence your code must use the Hibernate Transaction Manager, as it would in a standalone application environment 

With this in mind you&#8217;ll have to configure the server.xml with
  

```xml
<Resource name="jdbc/testdb" auth="Container" 
type="javax.sql.DataSource"
maxActive="100" maxIdle="30" maxWait="10000"
username="user" password="pwd" 
driverClassName="com.mysql.jdbc.Driver"
url="jdbc:mysql://localhost:3306/mydatasource?autoReconnect=true"/>
```

And web.xml with
  

```xml
<resource-ref>
<description>My datasource</description>
<res-ref-name>jdbc/testdb</res-ref-name>
<res-type>javax.sql.DataSource</res-type>
<res-auth>Container</res-auth>
</resource-ref>
```

Then your hibernate.properties configuration would look like:
  
```ini
hibernate.dialect=org.hibernate.dialect.MySQLInnoDBDialect
hibernate.connection.datasource = java:comp/env/jdbc/testdb
hibernate.transaction.factory_class = org.hibernate.transaction.JTATransactionFactory
```