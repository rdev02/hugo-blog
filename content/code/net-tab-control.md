---
title: .NET tab control.
author: Viktor Halitsyn
date: 2010-10-08T19:39:00+00:00
type: post
categories:
  - tech
tags:
  - .net

---
&nbsp;&nbsp;&nbsp; Consider some UI that you would like to present as tabs. And you also know that all these tabs would have a considerate amount of code in common. There are some thoughts [here][1] but those are more about visual representation. Also creating&nbsp; usercontrols I believe is not an option due to it&#8217;s not expected to have more tah one tab of a kind. The solution has to both utilize the VS designer(otherwise tab creation would take considerably more time) and keep the code separate from the tabcontrol &#8211; holder form. So that you have all functionality neatly separated. 
  
&nbsp;&nbsp;&nbsp; Haven&#8217;t found any except [here][2] which looks kinda OK but lots of custom code. Would like to have just tabs used just in normal tabcontrol&#8230;
  
&nbsp; &nbsp; Will try to find/develop one over weeks or find a good reason not to do it.(know WPF solves it with templates. requirements&nbsp; &#8211; plain old WinFroms) Any comments are welcome. 
  
&nbsp;

 [1]: http://stackoverflow.com/questions/210597/suggestions-for-a-c-custom-tabcontrol
 [2]: http://dotnetrix.co.uk/tabcontrol.htm