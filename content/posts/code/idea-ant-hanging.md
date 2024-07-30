---
title: Idea ant hanging
author: Viktor Halitsyn
date: 2010-02-11T10:18:00+00:00
categories:
  - tech
tags:
  - codesod

---
I had a very &#8216;uncomfortable&#8217; issue with Idea when using it for ant builds &#8211; it hang in certain places like jar update task, rename task or similar.
  
All my struggle can be found on [Idea track][1]. Fortunately Carl Abramsson found a workaround for that issue using

> -noinput 

switch to ant launch. Now everything works like a charm. Decided to create this post in case some one will be desperately googling for a solution in the wrong place 🙂

 [1]: http://youtrack.jetbrains.net/issue/IDEA-26198