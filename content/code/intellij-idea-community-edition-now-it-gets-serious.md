---
title: IntelliJ IDEA Community Edition – now it gets serious.
author: Viktor Halitsyn
date: 2009-11-12T10:41:00+00:00
type: post
categories:
  - tech
tags:
  - tools

---
&nbsp;&nbsp; &nbsp;In my <span></span>[<span></span>previous pos][1][<span></span>t<span></span>][2]&nbsp;I described some minors that harmed my first impressions on this perfect IDE, but now that I used it for a week more serious things came along.
  
&nbsp;&nbsp; First of all I&#8217;ve ran into an issue with ant Jar task. It refused to work with an &#8216;update&#8217; option set to true. The interesting part of it was that executing the task&nbsp;independently&nbsp;didn&#8217;t cause any pain, but launching it in the sequence(multiple calls through different build scripts) caused ant to &#8230; hang. It just waits indefinitely. I double checked if the file was locked &#8211; it wasn&#8217;t. I could do anything I want with the file &#8211; even delete it while ant was successfully hanging. Fortunately I resolved this by using a combination of &#8216;unzip&#8217;/&#8217;jar&#8217; tasks, but &#8220;it&#8217;s not the right way&#8221;.
  
&nbsp;&nbsp; Second thing that struck me was the ant editor. I felt really happy fixing build scripts in Idea so I recommended it to a&nbsp;colleague&nbsp;of mine. She tried it and the first time she opened the the build script &#8211; Idea hang with eating all of the CPU and a fair amount of ram. I thought it&#8217;s something like &#8216;things happen&#8217;. But it reproduced continuously. So I tried opening it by myself &#8211; and it also reproduced!!! Damn! Eclipse opens the file in a blink of the eye. The file is fairly big and complicated. But it is _impossible _to work with it in Idea.
  
&nbsp;&nbsp; Personally I don&#8217;t think I would experience any of these issues using the earlier 8.0 commercial version or 9.0 commercial. But these things might really ruin Idea reputation as a &#8220;great IDE&#8221; for people who haven&#8217;t used it and want to look at it while it&#8217;s available free.
  
&nbsp;&nbsp; The last but not the least &#8211; plugins. Even if it does work with 9.0 version, it may well crash your community version, so watch &#8211; out.

 [1]: http://revengedevelopment.blogspot.com/2009/11/intellij-idea-community-edition-first.html
 [2]: http://www.blogger.com/