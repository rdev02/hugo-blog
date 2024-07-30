---
title: .NET 1.0 to .NET 2.0 migration…
author: Viktor Halitsyn
date: 2008-10-01T09:24:00+00:00
categories:
  - tech
tags:
  - .net

---
Actually a painfull process it can be if the project you&#8217;re trying to convert has lots of custom stuff. The first problem i stumbled upon was resources. eah yeah &#8211; i mean form resources. They ciompletely refused to work. I know about that separation that came in when VS 2005 was introduced so I did use [DeCodEx ][1]to make majour conversion and splitting but it still turned out that my forms are not opening :(. So here  is what i did &#8211; i converted the resource files using &#8216;resgen&#8217; tool to and from *.resx files. Some of them twice. yeah <span style="font-weight: bold;">twice.</span> I don&#8217;t know why but it turned out that converting once may not help and making this twice solves the problem&#8230; Also don&#8217;t forget to reload VS every time you do such conversions &#8217;cause you might not see the resuult. I know that sounds odd but thats real life and experience and finally that worked out for me.

<div>
  Another awful thing i had to fight was error like <span style="font-style: italic;">&#8220;object &#8230;  returned null &#8230; but this is not allowed &#8220;</span>. After some googling and investigating i found the solution like this:
</div>

<div>
  properties which caused that eroor were decorated with
</div>

<div>
</div>

```cs
  [DesignerSerializationVisibility(DesignerSerializationVisibility.Content)]
```

<div>
</div>

<div>
  After removing this decoration all works good. There is quite a usefull <a href="http://msdn.microsoft.com/en-us/library/ms735923.aspx">article of this on MSDN</a>.
</div>

 [1]: http://coolsoft.altervista.org/DeCodEx