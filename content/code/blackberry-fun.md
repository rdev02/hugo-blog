---
title: BlackBerry fun.
author: Viktor Halitsyn
date: 2009-10-20T14:08:00+00:00
type: post
categories:
  - tech
tags:
  - mobile
  - BlackBerry

---
Reading the developers guide on BlackBerry you may come across astonishing things. I&#8217;m impressed. When I started doing  little things in the blackberry app I soon got used to it and considered the platform to be well organized. Now that I&#8217;m reading a book I get more and more confused about what I should do and what I shan&#8217;t! Take a look at this masterpiece

> <span style="font-family: Verdana; font-size: 13px;"></span>
  
> 
<span style="font-family: Verdana; font-size: 13px;"> 
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   <b>Why it is not necessary to obfuscate applications</b>
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   The compiler for the BlackBerry® Java™ Development Environment is set to minimize the size of the application.
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   The .cod file that results provides obfuscation-like services that are similar to those that obfuscation packages
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   provide in an effort to reduce the size of the .cod file. For example, the BlackBerry Java Development Environment
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   removes the following information from a .cod file:
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   • all debug information
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   • local variable names
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   • source Line Numbers
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   • private method and member names
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   <span style="color: red;">As such, RIM does not believe it is necessary for you to provide obfuscation for your applications in addition to the </span>
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   <span style="color: red;">existing obfuscation that, by default, the BlackBerry JDE provides for all applications. </span>In fact, Research In Motion
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   does not perform any additional obfuscation of its own products.
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   The BlackBerry JDE does not integrate support for obfuscation through third-party tools . <b><span style="color: red;">As such, you must </span></b>
> </div>
> 
> <div style="margin-bottom: 0px; margin-top: 0px;">
>   <b><span style="color: red;">include a command-line procedure to obfuscate .cod files for use on BlackBerry devices.</span></b>
> </div>
> 
> <p>
>   </span>
> </p></blockquote> 
> 
> <p>
>   So obfuscate or not? that is thew question&#8230;.
> </p>
> 
> <p>
>   Another one &#8211; comment to a method from SDK<br /> <span style="font-size: 13px;"><span style="font-size: small;"><b><i>Category:</i></b></span><span style="font-size: small;"><i>Framework: This element may be called by the underlying framework. Members that are invoked by the framework <b>may not behave exactly as documented</b>. </i> WHAT DA HELL???</span></span>
> </p>
> 
> <p>
>   And the last one so far
> </p>
> 
> <blockquote>
>   <p>
>     <span style="font-family: Verdana; font-size: 13px;"></span><br /> <span style="font-family: Verdana; font-size: 13px;"> 
>     
>     <div style="margin-bottom: 0px; margin-top: 0px;">
>       <div style="margin-bottom: 0px; margin-top: 0px;">
>         <div style="margin-bottom: 0px; margin-top: 0px;">
>           <b>HorizontalFieldManager </b>organizes fields horizontally. To enable horizontal scrolling, provide the
>         </div>
>         
>         <div style="margin-bottom: 0px; margin-top: 0px;">
>           Manager.HORIZONTAL_SCROLL style. If you do not include the HORIZONTAL_SCROLL parameter, the
>         </div>
>         
>         <div style="margin-bottom: 0px; margin-top: 0px;">
>           fields arrange themselves horizontally and can exceed the width of the screen, but users cannot scroll to
>         </div>
>         
>         <div style="margin-bottom: 0px; margin-top: 0px;">
>           content that is beyond the right side of the screen.
>         </div>
>         
>         <div style="margin-bottom: 0px; margin-top: 0px;">
>           BlackBerry devices <b>do not display horizontal scrolling indicators</b> or scroll bars
>         </div>
>       </div>
>     </div>
>     
>     <p>
>       </span>
>     </p></blockquote> 
>     
>     <p>
>        Just wait me telling the user &#8211; &#8220;This application is magic &#8211; you don&#8217;t see the scrollbars, but the content is there &#8211; believe it, or not&#8221; (rofl)&#8230;.<br /> Probably will edit this post later with more &#8220;funny stuff&#8221;<br /> &#8212;&#8212;&#8212;&#8212; and here it comes:)<br /> Nice title<br /> <span style="font-family: Verdana; font-size: 13px;"><i>Using HTTPS connections.</i></span><br /> <span style="font-family: Verdana; font-size: small;"><span style="font-size: 13px;">After this goes the detailed explanation how to code and use http(s) connections on BlackBerry. And then a small hint</span></span><br /> <span style="font-family: Verdana; font-size: 13px;">: <i>The BlackBerry Internet Service Browser does not allow Java applications to initiate HTTP, HTTPS, or TCP connections</i>. LOL.</span>
>     </p>