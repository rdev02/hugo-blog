---
title: Java to BlazeDS NAN in double values.
author: Viktor Halitsyn
date: 2011-04-22T09:07:00+00:00
categories:
  - tech
tags:
  - java

---
Internet is basically full of subj but vice-versa(BlazeDS to java). So the problem I had was that when the object is &#8220;translated&#8221; from java via BlazeDS to flex application it had NAN&#8217;s in the double fields. The transfer object was a simple POJO I passed via the interface. First thing I checked were the getters &#8211; all present and valid. I took quite a lot of time till we figured out that it was the missing setters which made the code malfunction. The problem was that on some objects I declared an immutable interface and implemented it in a DTO object. I did not add setters where I never planned to use them. This turned out to be the crucial mistake: **Always be sure to have both getters and _setters_ in your DTO.** It doesn&#8217;t matter if you pass it via immutable interface or not &#8211; the underlying object **has to have both methods on the field** otherwise we get NAN.