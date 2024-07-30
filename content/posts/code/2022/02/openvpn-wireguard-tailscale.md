---
title: 'VPN. Open? Wire? Scale?'
author: Viktor Halitsyn
date: 2022-02-22T22:22:22+00:00
categories:
  - tech
tags:
  - software
cover:
  image:  /images/2022/VpnPost.png
---

In this post I go about my journey into VPNs and conclude TailScale is the GOTO for anyone who needs a nice cheap connectivity tool asap.


## History
Sooner or later one needs a VPN. Various reasons:

* test out UX/behavior from a different part of the world
* access your test server DMZ, while away from your test server LAN
* verify prices in other country
* pay your bills through your bank, while abroad

Most people today, though, get it for privacy reasons. Most of the marketing around ISPs misses me entirely(as the majority which knows a thing or two about networking). I use VPN for strictly practical reasons:

* when the network can't be trusted(no that's not privacy, really, that's more, like, security :))
* when the resource I need can't be easily reached via current network

For those, in the olden days, I've tweaked an [OpenVPN based set of scripts][1] that would allow me to easily create an instance using some provider, like [DigitalOcean][2] or [AWS][3]. Within 20 minutes I'd have a living OpenVPN connection, based on a data center of choice. I still use this solution from time to time, but it's quite inconvenient for a number of reasons:

1. It takes gd long 20 minutes.
2. Creation requires transmission of sensitive information via network, which, by definition, isn't trust-worthy.
3. Cleanup is necessary later, unless infinite credits or money are assumed(In that case points 1 and 2 do not apply either - one could just pre-create a bunch of instances and switch between them on-demand).

Both time and cost are of issue, but since my VPN needs are very infrequent, I made do with those, planning ahead(and not forgetting to clean up later). If it works - do not break it :) 

Time has passed and I figured that a strategically placed raspberry pi solves 98% of my use cases, costs nothing or next to nothing to maintain and is even easier to run [PiVPN][5] on it. So it has been a while since I used above set of scripts for anything practical.

Finally recently I started hitting some serious network speed issues with OpenVPN. That got me looking around again.

## WireGuard
Enter [WireGuard][4]. This solved my speed issue and is still very easy to set up with raspberry pi: you can use the same [PiVPN][5] project! 
You can even have both on the same pi: just different ports. Very convenient to do side-by-side testing. As a perk: WireGuard did wonders for my [MicRouter][6]. Btw, I really recommend this thing as your travel companion for those places that would charge you extra for each mac address used to connect to WiFi!
I won't post my speed tests, looks like plenty of [people did that before me][7] :)
However since I was looking at the market one more tool caught my eye...

## [TailScale][9]

I first heard about this tool listening to a tech podcast. It sounded promising, especially the [exit node][8] functionality looked appealing. 
```
brew install tailscale
```
authorized via GitHub and viola - TailScale is running on [my new M1 Pro Max][10].

```
sudo apt install tailscale
```
and it's running on my pi! literally minutes to get this set up and test connectivity: it just works. 
Next testing the exit node... that has hit a snag. My [internet just stopped working][11]. Awesome, time to test out documentation and support forums! Documentation is solid, I've found some bits of wisdom here and there, added
```
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
```
did not help the issue. But after a couple of weeks, reports and 3 minor versions later it's working with no issues.

**Wait**. Did I mention that [it's free][12] for personal use?

**Wait, wait**... Did I mention that the code is available on [GitHub and it's Go + WireGuard][13]?

The speed of this solution using my mac and pi is still for some reason below my native pi/wireguard/tunnelblick combo, but judging by the way this company grows and addresses issues: I foresee good this coming from them.

**In summary**

While PiVPN/WireGuard is still my to-go tool for personal VPN needs, TailScale grows quickly on me for it's ease of deployment, use and versatility.

 [1]: https://github.com/rdev02/docker-openvpn
 [2]: https://www.digitalocean.com/products/droplets
 [3]: https://aws.amazon.com/ec2/
 [4]: https://www.wireguard.com
 [5]: https://pivpn.io
 [6]: https://www.gl-inet.com/products/microuter-n300/
 [7]: https://duckduckgo.com/?q=wireguard+openvpn+speed+test&t=osx&ia=web
 [8]: https://tailscale.com/kb/1103/exit-nodes/
 [9]: https://tailscale.com
 [10]: /code/2021/12-06/macbook-pro-max-migration/
 [11]: https://github.com/tailscale/tailscale/issues/2654
 [12]: https://tailscale.com/pricing/
 [13]: https://github.com/tailscale/tailscale