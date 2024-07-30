---
title: Windows service accounts.
author: Viktor Halitsyn
date: 2009-08-21T09:14:00+00:00
categories:
  - tech
disqusIdentifier: 5353345949
tags:
  - windows

---
Recently(probably 2 moths ago, but still &#8211; recently) had to configure a bit of security for our test servers, visible to the web. Of course started with securing services under their own special accounts, limited to a very small amount of rights. Everything went on smoothly except one lil&#8217; thing &#8211; it didn&#8217;t work:) All permissions were set correctly, all relationships assigned well,  but services didn&#8217;t start. Resolution(found it in 2 hours) was fairly simple &#8211; while creating accounts for my services I forgot to check the &#8220;Password never expires&#8221; checkbox. Probably Windows wanted to change the password and the service didn&#8217;t expose a &#8220;free will&#8221; for changing pwds so that the whole authorization process failed. Technically it&#8217;s my fault, really &#8211; stupid case:(