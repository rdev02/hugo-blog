---
title: WP7 Mango dive
author: Viktor Halitsyn
date: 2012-02-26T16:15:00+00:00
categories:
  - tech
disqusIdentifier: 5154601956
tags:
  - rant
  - wp

---
<div dir="ltr" style="text-align: left;">
  It has been a few month since I started mangling with WP7 platform. The more I learnt about it from the inside the more I wanted to buy new real WP7 phone. Things changed slightly after I have done my very first app(didn&#8217;t make it to the app store as I found plenty of similar ones already there and mine was not adding much :() but still as of now I would really be interested in posessing a WP7 phone and giving it a solid try. But let me start from the beginning&#8230;</p> 
  
  <p>
    <b>Learning</b>
  </p>
  
  <p>
    As my primary learning resource I used 2 jumpstart sessions : <a href="http://channel9.msdn.com/Blogs/egibson/Windows-Phone-7-Jump-Start-Session-1-of-12-Introduction">WP7 jumpstart</a> and <a href="http://channel9.msdn.com/Series/Mango-Jump-Start/Mango-Jump-Start-01-Building-Windows-Phone-Apps-with-Visual-Studio-2010">Mango jumpstart</a>. If you have the time to invest into watching those I highly recommend them as these are very good sources of information to study the platform fast. Especially keeping in mind that all the sessions there are accompanied by code examples which are also easily found on <a href="http://www.codeplex.com/">codeplex</a>. The code especially nice due to the availability of the before-after versions of demo applications so that it is easy to see the transition from incomplete/incorrect state to the solid code.
  </p>
  
  <p>
    <b>Environment</b>
  </p>
  
  <p>
    <a href="http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=27570">Windows phone SDK 7.1</a> integrates seamlessly into VS2010 and makes a very pleasant environment to work with. The emulator gives you a lot of power when debugging your app. Still I missed the camera support(it is there but I kinda didn&#8217;t like it) and the FS support. It&#8217;s not so easy to look at things located on the Isolated storage as well as on the SD card. Otherwise there&#8217;s everything you&#8217;d want from the emulator, including recording gyroscope and accelerometer changes and playing them afterwards again and again.
  </p>
  
  <p>
    <b>Development</b>
  </p>
  
  <p>
    Developing<b> </b>WP7 is tricky yet interesting. You&#8217;re using almost all of what .NET can offer with certain limitations(e.g. AsyncCallback is almost useless) seamlessly and easily. There&#8217;s a lot you can customise in the standard controls collection. Still some common things which I would expect to be just working are in fact not. As an example I cannot grasp why would one want the Popup class if it still has to be injected into the visual tree <i>manually</i>&#8230; Why not use the panel with visibility instead? It&#8217;s a lot more natural to me than injecting the Popup somewhere at runtime&#8230; Another interesting discovery for me was the fact that IsolatedStorage is <i>not</i> available in the OnNavigatedTo event. I guess MS guys give us a hint here that we should probably use only the StateStorage as it is much faster, and then use progress indicators to load heavy stuff after the page is loaded&#8230; but then why would I bother using StateStorage at all? Just put the &#8220;loading&#8230;&#8221; progress on top in the first place and get all the stuff from the IsolatedStorage where it is grouped together nicely and not scattered between 2 storage places(not to mention the fact that when storing to StateStorage you are highly recommended to store things to the IsolatedStorage as well as the state might not be available if the application was killed in the meantime..)<br /> Nevertheless there&#8217;s a lot to admire here as well&#8230; Among other things I liked the controls customisation ability and overlaying concept best. WYSIWYG designing is really helping out here cutting redeployment and testing time. Debugging the real phone is easy too.
  </p>
  
  <p>
    <b>Bottom line</b>
  </p>
  
  <p>
    WP7 development surely has its own intricacies one might not like, but for me this is what makes mobile development thrilling. You can discover completely new ways of using the very old concepts/controls and rethink your strategies towards certain solutions. Mobile changes the way you treat data and UI and Metro-powered<b> </b>mobile development really broadens ones vision on how the data can be processed and delivered to utilise the customer needs.</div>