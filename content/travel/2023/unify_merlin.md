---
title: 'SNB: Unify Dream Router vs. Merlin-Compatible Router'
author: Viktor Halitsyn
date: 2023-12-11T06:20:18+70:00
type: post
categories:
  - code
tags:
  - snb, router
thumbnailImage: /images/2023/AsusUdr.png
---

In this post I go over my move to Unify router, over a top-of-the-line Asus one, even though I got a new one just about a month ago.

I used to like my Asus routers: they stroke a balance between performance, reliability user-friendliness and maintenance. "Balance" is a very important word here, because they did neither well. In fact, as my routing needs grew, even "well" became an issue.

Commonly I'd need to run a couple networks: main network for all secure devices, guest network for, well, guests :), isolated guest network for all the non-US-made IoT thingies and one for gaming/mining/whatever. Now, for convenience, I'd like my printer to be available on all of them, I'd like my main network devices to be able to access my IoT devices, but not the other way around, I'd like my gaming to be isolated and I'd like my guests to be isolated, limited, but also able to "talk to" IoT. Oh, and yea - I'd like to have a custom domain VPN, that would bring me into the main and guest network, but not the gamin or IoT one. 
Easy, and not very complicated, right? Right ?!

Achieving this with with native administration interface was possible, but a mess. It required using [Merlin][1], [YazFi][2] and custom routing. And even with that, VPN was constantly malfunctioning, so I moved to have it on a separated raspberry pi. WiFi was separated into 2.4 and 5Ghz bands, so instead of having 4 networks I had to manage 8 and any change in DHCP would requite a full router restart and reset.

While dealing with another "networking" issue, that, as usual turned out to be a bug in the firmware, I stumbled upon the [Dream Router][3]. I heard a lot of good things about the company before from the podcasts I listen to, so I decided to just give it a closer look, and shortly thereafter - a try.

I'm so impressed. Finally, a routing product for a small home network enthusiast, that is easy to understand, configure and maintain.

Benefits of the machine:

1. DHCP rules applies without restart;
2. automatic firmware update on schedule + automatic backup of configuration;
3. 2.4 Ghz networks are "merged" with 5Ghz ones: device is "handled" on the best band it supports automatically, using one SSID
4. You actually define networks and network(firewall/routing) rules rather then assuming that a particular SSID is a particular network. Thus you can apply same rules to networks, not AP/SSIDs.
5. Port management and expansion out of the box.
6. 1-click multi-site VPN
7. network packet scanning and traffic rules(much better then QoS) out of the box.
8. Network(wifi/WAN/client) health monitoring and alerting out of the box
9. and much more.... :)

All of that for a quite meager price.
Now, granted top-of-the-line Asus router would be more powerful in terms of transmission and coverage rules, but it would also cost more and I actually rarely use it's limit speeds.

The throughput of Dream Router turned out to be more then enough for my daily needs and the simplicity of configuring and maitaning this piece of hardware is, so far, unmatched for me.

I'm so happy I found it and I wholeheartedly recommend it to anyone who's still out there, struggling with their clunky hardware to deliver simple results most household need today.

 [1]: https://www.asuswrt-merlin.net
 [2]: https://github.com/jackyaz/YazFi
 [3]: https://store.ui.com/us/en/products/udr