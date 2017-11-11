---
title: Avahi and “network service discovery disabled”
author: Viktor Halitsyn
date: 2011-05-20T21:07:00+00:00
type: post
categories:
  - tech
  - os
disqusIdentifier: 6079234188
tags:
  - codesod
  - linux

---
Had a real problem with my wifi after moving to Ubuntu from win -> my download speed was dead slow with ~2Mb/s. Tried various things including [this][1] [this][2] and finally [this][3]. The last one did the trick. It was Avahi which spoiled everything. The thing that led me to Avahi was this irritating message popping out each time I connected:

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a href="" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="119" src="" width="320" /></a>
</div>

Having this changed in

> _AVAHI\_DAEMON\_DETECT\_LOCAL=1 to AVAHI\_DAEMON\_DETECT\_LOCAL=0 in   
> /etc/default/avahi-daemon_

Got me up to 22Mb/s after reboot. We&#8217;ll see if I could squeeze more from my cheap router flashing it with some different firmware, but for now I&#8217;m quite happy to gain a 10x speed improvement 🙂

 [1]: http://ubuntuforums.org/showthread.php?t=1596769
 [2]: http://lokir.wordpress.com/2010/11/08/slow-wifi-problem-solved-ubuntu-10-04-10-10/
 [3]: http://ubuntuforums.org/showthread.php?t=1282321