---
title: '907: Invalid cod and other BlackBerry OTA problems.'
author: Viktor Halitsyn
date: 2010-03-10T10:37:00+00:00
categories:
  - tech
  - code
tags:
  - mobile
  - BlackBerry

---
    Most of us, who developed any BlackBerry app that had to be shipped OTA had seen this &#8220;descriptive&#8221; error. It might be anything from invalid jad to signing problems. The one I&#8217;m going to describe here is when the size of the cod file is more than 64K.
  
   Finding out if you have this problem is fairly easy: just look into the final cod and see if there are actually 2 cod files packaged in one cod. Can be easily done via renaming to .zip and extracting. Other things that would show you smth. has gone wrong:

  * <appname>-1.cod files are being generated</appname>
  * <appname>-1.debug files. Cod might be ok, so may be the jad file. But still will get 907.</appname>
  * *-1.cod references in ad files.
  * nested cod files as already mentioned.

<div>
  The general approach to fix this issue is creating application libraries and shipping them linked along with application. This is more easily said then done, because decoupling your app when it&#8217;s already working might be a tedious job to do. Moreover when you seem to have completed what is needed you might see that you&#8217;re 2-5 Kb over limit and that really sucks. But that is not all of the &#8220;presents&#8221; you&#8217;ve got with RIM. 
</div>

<div>
  Let us say you&#8217;ve mastered your app in a way that it is shipped with few libraries and it work OK. Now requirements have come to use a newer JDE/add some features. First thing to check would be just to compile using the new JDE. Although you won&#8217;t probably have any issues with compiling it is VERY likely that you&#8217;ll get larger cod files as result. I tested with JDE&#8217;s from 4.0.2 till 4.7. And every new version compiles more code then the previous one. One exception with 4.6 &#8211; it compiles the larges binaries whatsoever :). So you&#8217;re back to your previous job &#8211; refactoring the source code:)
</div>

<div>
  In order for these things to happen less often use these approaches when developing with RIM:
</div>

<div>
  <ul>
    <li>
      Avoid nested classes. Each Nested class would enlarge the original class file and produce and extension worth at least 1.1K
    </li>
    <li>
      Avoid public static or just public/protected methods/properties. Generates huge amount of header bytecode.
    </li>
    <li>
      Avoid anonymous classes. Each anonymous class would result in <classfilename>$N.class where N is the number of your anonymous class:) Each worth at lest 0.9K. If you structure your code to use existing classes, you&#8217;ll reduce the output bytecode significantly.</classfilename>
    </li>
    <li>
      If the nested class has to be used &#8211; try to use anonymous instead. You&#8217;ll gain about 0.5 K on each.
    </li>
    <li>
      RIM doesn&#8217;t like generics. Full-stop
    </li>
    <li>
      Try to avoid using interfaces. If, for example, the base class implements and interface and the child one also states that &#8211; you&#8217;ll get double same bytecode. From my experience with RIM I&#8217;d better use classes(if applicable) and only then interfaces. The overhead is tremendous.
    </li>
  </ul>
  
  <div>
    Happy development days with RIM 😉
  </div>
</div>