---
title: Devenv crash from the command line.
author: Viktor Halitsyn
date: 2009-12-24T21:11:00+00:00
type: post
categories:
  - tech
tags:
  - .net
  - ms

---
&nbsp;&nbsp; &nbsp; &nbsp;Those of you who use devenv for building VS solutions(although you are discouraged to [here][1]) might get into trouble if your solution has more than 1 project and it takes quite a time building both. Today I had a problem with that launching VS build from inside an ant script &#8211; a standard windows crash appeared with 0xc000005 error code and a &#8220;friendly&#8221; Send/Don&#8217;t send error to Microsoft dialog. Also I noticed that the VS cl.exe &nbsp;compiler process is hanging after this crash, waiting for infinity.
  
&nbsp;&nbsp; &nbsp;Googling around led me to [this hm&#8230; page][2]&nbsp;&nbsp;after I thoroughly read the [very one microsoft forum thread about the issue][3]. The culprit is that you have to use 1 thread for all your compiling. Reposting from the last link you have to

> <span style="font-family: Verdana; font-size: 11px;">set &#8220;maximum number of parallel project builds&#8221; to 1 instead of 2 or more, it doesn&#8217;t crash anymore.</span>

explanation &#8211; a memory leak in the building module&#8230;
  
&nbsp;&nbsp; &nbsp; This actually doesn&#8217;t have to reproduce in SP1. I&#8217;ve got the latest VS Team developer with the latest SP and it still reproduces&#8230; Will try msBuild tomorrow and see if situation improves having more threads as the way it had been mentioned in the above links &#8211; if you have multi-core architecture you probably don&#8217;t want to do everything in one thread, especially if this is your continuous integration server.

 [1]: http://msdn.microsoft.com/en-us/library/xee0c8y7.aspx
 [2]: https://connect.microsoft.com/VisualStudio/feedback/ViewFeedback.aspx?FeedbackID=332669
 [3]: http://social.msdn.microsoft.com/Forums/en-US/vssmartdevicesnative/thread/993b29f4-d8f5-4c2a-8b1a-4b130ed01dc4