---
title: j2ee sdk install hanging fix.
author: Viktor Halitsyn
date: 2011-06-01T18:41:00+00:00
type: post
categories:
  - tech
tags:
  - codesod
  - java

---
Spent about 3 hours trying to install the latest [java ee sdk][1] on my natty narwhal with no luck. It usually hang with 45% and 5seconds left forever. Might be I&#8217;m a bad googler but turned out that there [is a bug in the installer][2] which prevents the installation from continuing. The workaround is to **disable the network connections before the installation** begins(disabling while it is hanging did not work for me). You won&#8217;t have the Update
       
Tool Bootstrap tool configured but still you&#8217;ll have the sdk installed and ready for your tests/work.

 [1]: http://www.oracle.com/technetwork/java/javaee/downloads/index.html
 [2]: http://markmail.org/message/xtjq2rvka2qd3drn