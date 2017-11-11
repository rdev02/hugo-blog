---
title: Javascript performance tests
author: Viktor Halitsyn
date: 2012-08-02T22:35:00+00:00
type: post
categories:
  - tech
disqusIdentifier: 5154577652
tags:
  - js
  - perf

---
<div dir="ltr" style="text-align: left;">
  I&#8217;m dealing a lot with javascript nowadays and obviously I have to take performance into account. One way of doing this is writing a bunch of test code and record the time for your scripts, but what if you wanted to quickly test out some approaches and do not really want to invest a lot into them? Today I was pointed to a solution:&nbsp;<a href="http://jsperf.com/">http://jsperf.com/</a>. This awesome tool does not just give you the framework to easily test out javascript but also provides and already created library of tests other people created when investigating their performance issues. Sweet! I finally have answer to <a href="http://jsperf.com/o-vs-a-access">this</a>&nbsp;question. And many more 🙂<br />It also quite clear that when dealing with math caching <a href="http://jsperf.com/local-variables-vs-array-access">arrays is a must</a>.
</div>