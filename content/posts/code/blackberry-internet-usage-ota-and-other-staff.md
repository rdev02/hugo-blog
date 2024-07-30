---
title: BlackBerry internet usage, OTA and other staff.
author: Viktor Halitsyn
date: 2009-09-24T11:43:00+00:00
categories:
  - tech
tags:
  - mobile

---
Continuing the BlackBerry theme would like to share a few more issues I&#8217;ve ran into recently.

  1. Internet usage.  
    If you subscribe to blackberry service you can optionally buy some &#8220;internet package&#8221; from your provider in order to minimize internet expenses. This a great choice since you&#8217;re going to visit internet pretty often if you&#8217;ve chosen blackberry. Everything goes fine until you start using Java apps on your phone. Java uses it OWN predefined internet connection DIFFERENT from the one the browser uses. Thus don&#8217;t be surprised to receive bills in the end of the month for internet besides your prepaid one. Currently I haven&#8217;t found a solution to workaround that and doubt that there is one. 
  2. OTA  
    When compiling your Java app to cod files watch out for .debug files. If you&#8217;ll notice some than you&#8217;re most likely to have OTA downloading problems with &#8220;Error code 97&#8221; &#8211; invalid cod file. The reason for this is that you&#8217;ve reached the byte limit for one library/cod. In BB you&#8217;re not allowed to send more than N(differs from JDE to JDE) bytes of data in one cod OTA. Workaround for that is splitting code into libraries and linking them. ALSO JDE may split your cod files into [name]-1,[name]-2 .cod files. The reason and solution for this is the same. 
  3. Research RIM libraries &#8211; they have a lot of peculiar things developed tat can really help you out when &#8220;reinventing the wheel&#8221;.