---
title: 'java.net.MalformedURLException: no protocol: and]'
author: Viktor Halitsyn
date: 2011-09-06T08:06:00+00:00
type: post
categories:
  - tech
  - code
disqusIdentifier: 5154636256
tags:
  - codesod
  - java

---
Had a problem with injecting JMSFactory for JBoss MQ using spring(for Camel :)). The problem had the next StackTrace:

```
Caused by: javax.naming.CommunicationException [Root exception is java.rmi.ServerException: RemoteException occurred in server thread; nested exception is:
java.rmi.UnmarshalException: error unmarshalling arguments; nested exception is:
java.net.MalformedURLException: no protocol: and] at org.jnp.interfaces.NamingContext.lookup(NamingContext.java:780)
at org.jnp.interfaces.NamingContext.lookup(NamingContext.java:627)
at javax.naming.InitialContext.lookup(InitialContext.java:392)
at org.springframework.jndi.JndiTemplate$1.doInContext(JndiTemplate.java:154)
```

I was sure I configured it right as I have

```xml
<jee:jndi-lookup id="JBossMQ" jndi-name="XAConnectionFactory" proxy-interface="javax.jms.ConnectionFactory" environment-ref="jndiProps" resource-ref="true"/>
    <util:properties id="jndiProps" location="META-INF/spring/jndi.properties"/>
```

And with those jndi.properties I was sure I have to get a refference as I could do that using the InitialContext
by hand&#8221;. **Turned out that it was maven that made things go wrong. moving the repository to a path with no spaces solved the issue**(e.g. c:mavenrepo).
  
Hope this helps someone.