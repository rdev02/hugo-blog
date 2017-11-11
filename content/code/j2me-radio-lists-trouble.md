---
title: J2ME radio lists trouble
author: Viktor Halitsyn
date: 2009-03-19T13:05:00+00:00
type: post
categories:
  - tech
tags:
  - java
  - mobile

---
Recently I needed to do a simple task &#8211; implement a radio button list with choices under J2ME MIDP 2.0. I started with creating an new view of type javax.microedition.lcdui.List, keeping in mind that I can just construct it with List.EXCLUSIVE and add whatever items i want and everything should work fine. Well almost&#8230; All looked nice and the items showed up very attractively, having transparency and current font set up by default, which looked quite impressive.
  
But than our quality assurance posted me a &#8220;small&#8221; bug. They said &#8211; after navigating from and to the screen selected item looses focus and moreover if the first item is not selected and you navigate from and to he screen you&#8217;re not able to select first choice until you move selection up and down&#8230;. What a tiny thing i thought&#8230; Should be a 5 minute fix. But when I investigated further it occurred that there is no method setting current highlighted item in list. Could be accomplished by Displayable.setCurrentItem() but this is reported to be buggy on some phones! What a mess!
  
Ok i thought &#8211; wise man couldn&#8217;t leave it just like that &#8211; it&#8217;s very annoying form the users point of view. So looked forward to the standard radio-list approach and found javax.microedition.lcdui.ChoiceGroup. Which in-fact worked just perfectly with one flaw &#8211; it can be placed only on a form and thus has its own control background(not transparent). Apparently non-transparent background is better than &#8216;buggy functionality&#8217; in fancy list. People need software that works perfect(and looks perfect too but works comes first). Hope that thing will be fixed in new releases of MIDP. BTW heard they are going to implements impressive things there&#8230; Just can&#8217;t wait seeing &#8217;em in action 🙂