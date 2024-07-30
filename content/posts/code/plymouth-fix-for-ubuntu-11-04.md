---
title: Plymouth fix for Ubuntu 11.04
author: Viktor Halitsyn
date: 2011-06-28T20:22:00+00:00
categories:
  - tech
tags:
  - linux
  - ubuntu

---
If you&#8217;re in the same trouble as I was with black screen during boot(plymouth has the theme installed and even displays it during shutdown) then there is a small thing that has to be edited in order to get rid of this annoyance. Go to

>  /etc/grub.d/10_linux 

and remove the **handoff=7 **parameter. Inspired by [this][1] and also [this][2](pretty nice tool for configuring plymouth for dummies).

 [1]: http://www.webalice.it/bernardi82/software/fixplymouth-natty
 [2]: http://zorin-os.webs.com/splashscreenmanager.htm