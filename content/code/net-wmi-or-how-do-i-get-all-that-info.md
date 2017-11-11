---
title: .NET WMI. Or how do I get all that info?
author: Viktor Halitsyn
date: 2010-10-13T15:06:00+00:00
type: post
categories:
  - tech
tags:
  - .net

---
   There&#8217;s a ton of Win properties that your program might want to access/possess/transform and there&#8217;s a single place to find them: WMI queries. Though some of the properties might be scattered through different classes like Environment and \*Info and even \*Stream and more(and I believe accessing them through that places is significantly cheaper and faster) there&#8217;s one sigle silver bullet to get them all via 

> Select * From [win_category]</blockquote> 
> 
>      The problem is there are tons of &#8220;win_category]&#8217; and * values. Luckily there is a [WMICodeCreator][1]. Actually the tool is pretty self explanatory &#8211; just a few tips:
> 
>   * instead of using Select * you can use Select [ActualPropertyNameYouWant] which I believe is better
>   * instead of tedious browsing through combobox-ui use the text search on the code file supplied in the package
> 
> All Win properties nicely, fast and reliably right into your BL.
  
> <span style="font-size: xx-small;">do-not-forget-to-use-post</span>

 [1]: http://www.microsoft.com/downloads/en/details.aspx?familyid=2CC30A64-EA15-4661-8DA4-55BBC145C30E&displaylang=en