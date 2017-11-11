---
title: Wireless router problem and an unexpected way to solve it.
author: Viktor Halitsyn
date: 2010-03-09T22:38:00+00:00
type: post
categories:
  - tech
tags:
  - errata

---
A lil&#8217; bit nerdy here. So if anyone does not like all that computer stuff skip to the LAST LINE.  
_The trouble._  
It all started when I bought my first wireless router [D-Link DIR 320][1]. It looked fine. Started working in seconds and was pretty easy to configure. I was really happy but the I suddenly saw that I can&#8217;t reach my favorite finance site. Sites may be down, I thought. the next day I checked out at work and the it revealed to working just fine. But back at home I couldn&#8217;t reach it again. I looked as it was downloading it forever..  
_The struggle_  
I played with DNS, learned that google&#8217;s DNS is todays probably the most reliable one and easy to remember &#8211; 8.8.8.8. Pinged the websites but nothing really helped. Even if I was able to reach the main page after numerous pings &#8211; it all died just after browser/router reboot.  
I asked network administrators and friends but noone could give me a real solution. the best answer I&#8217;ve heard &#8211; do you really need those sites? Forget &#8217;em &#8211; everything else is working just fine 🙂&nbsp;Marvelous&nbsp;🙁  
_The Solution_  
I posted my problem on one of the network community threads. After standard advises to update firmware and disable the firewall I was recommended to change the MTU. To a value of 1480. Magic number.  
I changed it and it worked. **BUT **I will never be satisfied with a short win:) I began digging to understand the &#8220;magic&#8221;. So the first post I found was&nbsp;<http://forums.techguy.org/networking/475606-dsl-download-speed-sucks.html>. Using it found out that actually the working setting of 1480 produces next awful things:

C:WINDOWSsystem32>ping yahoo.com -f -l 1480

Pinging yahoo.com [69.147.125.65] with 1480 bytes of data:

Packet needs to be fragmented but DF set.  
Packet needs to be fragmented but DF set.  
Packet needs to be fragmented but DF set.  
Packet needs to be fragmented but DF set.

Ping statistics for 69.147.125.65:  
&nbsp;&nbsp; &nbsp;Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

And following the article&#8217;s advice:

C:WINDOWSsystem32>ping yahoo.com -f -l 1452

Pinging yahoo.com [69.147.125.65] with 1452 bytes of data:

Reply from&nbsp;69.147.125.65: bytes=1452 time=16ms TTL=58  
Reply from&nbsp;69.147.125.65: bytes=1452 time=14ms TTL=58  
Reply from&nbsp;69.147.125.65: bytes=1452 time=16ms TTL=58  
Reply from&nbsp;69.147.125.65: bytes=1452 time=15ms TTL=58  
Ping statistics for&nbsp;69.147.125.65:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 14ms, Maximum = 16ms, Average = 15ms

What is the optimum MTU for YOUR network is still a question &#8211; I found mine to be still 1480 :D. But I think this might be worth remembering for the future &#8220;practices&#8221;

LAST LINE: if you can&#8217;t reach some websites after installing the router &#8211; try changing MTU in router connection properties to 1500 or 1480.

 [1]: http://www.retrevo.com/s/D-link-DIR-320-Wireless-Routers-review-manual/id/23273ag014/t/1-2/