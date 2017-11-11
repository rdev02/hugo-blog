---
title: org.springframework.oxm.jaxb.Jaxb2Marshaller
author: Viktor Halitsyn
date: 2010-09-29T15:45:00+00:00
type: post
categories:
  - tech
tags:
  - java

---
There is a nice tutorial [here ][1]on how to create spring web services using jax-b. Works just fine but it&#8217;s hell lots of issues you would run into while making it actually work. The most frustrating for me as of today was the absence of _**Jaxb2Marshaller**_. in the std **&#8220;spring-ws-core&#8221;** artifact package. It&#8217;s just not there and noone would tell you. Only _**Jaxb1Marshaller **_is. After an hour googling found [this ][2](BTW very simple and straightforward) post where spotted the **&#8220;spring-oxm-tiger&#8221;** artifact. This is your saver if you you&#8217;re tearing your hair out seeking&nbsp; for the beloved interface. Maybe it&#8217;s just late today on the other hand&#8230;

 [1]: http://static.springsource.org/spring-ws/sites/2.0/reference/html/server.html#server-automatic-wsdl-exposure
 [2]: http://www.codemonkeyramblings.com/2009/11/a-basic-straight-forward-tutorial-on-spring-web-services/