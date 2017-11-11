---
title: IntelliJ IDEA Community Edition first impressions.
author: Viktor Halitsyn
date: 2009-11-05T10:15:00+00:00
type: post
categories:
  - tech
tags:
  - tools

---
&nbsp;&nbsp; &nbsp;Recently JetBrains [released the brand new version of IDEA][1] free for all. A few years before I Used this IDE for some projects and was very happy with it &#8211; consequent moving to eclipse&nbsp;disappointed&nbsp;me much. However time passed and I&#8217;ve got used to eclipse &#8211; it&#8217;s not a bad platform after all and it was free. So now when the free version idea came out I was very anxious convincing our customers to switch. Our system uses Ant for all building and and we&#8217;re used to remote debugging &#8211; thus all these fancy features left behind in community version didn&#8217;t scare me off. It also has to be mentioned that we do quite a bit of mobile development and I thought J2ME plugin available would do just right.
  
&nbsp;&nbsp; However things went on not just as fine as I&#8217;ve expected them to. Firstly this J2ME plugin refused to work crashing so seriously, that I had to reinstall the IDE. Moreover after uninstall/install IDE refused to launch at all. I had to remove it once again, restart and then install it from scratch &#8211; then it worked. Also I noticed IndexOutOfRange exceptions happening when using &#8216;External libraries&#8217; feature in the project tab. Of course they do not happen all the time &#8211; you have to do some quick combination of adding/removing/updating libraries, but still &#8211; it&#8217;s not the way of &#8220;the most intelligent IDE&#8221;.
  
&nbsp;&nbsp; I was most disappointed with menus in project settings dialog &#8211; they just refuse to work after some actions: add new library button, which normally results in &#8220;Add new library&#8221; dialog starts presenting 2 menu item one of which is &#8220;add new library&#8221; and the second is &#8220;empty&#8221; 🙂 none clickable.
  
&nbsp;&nbsp; However all these issues can be solved reloading the IDE which saves the day as the IDE is fast. Of cource all the standard IDEA features related to code completion, hinting and other &#8216;helping the developer to master his/her masterpiece&#8221; works perfect and to my mind &#8211; way better then the same thing in Eclipse.
  
&nbsp;&nbsp; To sum up I would say that I expected to see just another state-of-the-art IDE and it really turned out to be one. Except that it&nbsp;disappointed&nbsp;me quite a number of times :). Since these things were not critical and Eclipse also likes to crash suddenly &#8211; I&#8217;ll stick to this IDE, hoping the JetBrains will solve these problems in the nearest &#8220;patch&#8221;. If they don&#8217;t&#8230; well i think they will 🙂

 [1]: http://www.jetbrains.com/idea/nextversion/free_java_ide.html