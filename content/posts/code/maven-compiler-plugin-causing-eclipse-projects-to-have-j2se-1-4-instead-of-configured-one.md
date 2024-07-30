---
title: maven-compiler-plugin causing eclipse projects to have j2se 1.4 instead of configured one
author: Viktor Halitsyn
date: 2011-04-21T10:52:00+00:00
categories:
  - tech
tags:
  - maven

---
Today was investigating the warnings around some of my eclipse maven projects and noticed that I have a few
  


> There are no JREs installed in the workspace that are strictly compatible with this environment

messages. It took some time to figure out that it was actually the [maven-compiler-plugin][1] causing the the J2SE version to be picked up correctly. If I changed the packaging of the maven project to jar everything was generated fine. After some searching it turned out there is a bug with the generation tool [m2eclipse][2] I use. As [described here][3] the bug was fixed **TODAY** by **removing the java nature from the project**, which in turn removes the .classpath necessity which in turns removes the warnings. Few errors less &#8211; great work m2eclipse :).

 [1]: http://maven.apache.org/plugins/maven-ear-plugin/usage.html
 [2]: http://www.blogger.com/m2eclipse.sonatype.org/%20
 [3]: https://issues.sonatype.org/browse/MECLIPSEWTP-67