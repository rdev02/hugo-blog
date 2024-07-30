---
title: 'Smart home automation.'
author: Viktor Halitsyn
date: 2022-10-27T12:32:12+00:00
categories:
  - tech
tags:
  - software
cover:
  image:  /images/2022/WinkAeotecPost.png
---

Long time ago smart homes were hyped and the next generation of home automation. Beyond your analog timers and motion sensors, wouldn't it be nice to have something a tad more sophisticated to manage all these signals? For the last couple years I've been growing my smart devices set and hopping between platforms. This post summarizes the journey, currently at Samsung [Aeotec Hub][1] stop.

## In place of intro...

The moment one has an electrical device or switch, 2 maintenance problems arise, somewhat tied to each other:

* switch operation, and as a result of it:
* electricity costs

In the olden days devices were quite simple and we generally turned them on when necessary and then turned them off when not in use. Even at that time, perceptive of us would wonder: "can't the lights just come on when it's *not enough light*(i.e. dark?)? Or "wouldn't it be great if the thing just shut itself off once I'm not there?".

People solved this problem in one of 3 ways:
- have someone else do it for ya: a maid or else would turn on lights at dusk, and switch off things when everyone is in bed.
- have things on all the time: for the most part, you do not notice electricity-powered devices being on during day time, but it's obviously a waste and a drain on the grid/budget, hence...
- constantly obsess about having things on/off only when they are needed/used.

It takes a surprising amount of cognitive load to keep the most recent state of all the switches you interacted with in your mind. I mean, it's an exercise for the brain, but it's not one we evolved to solve efficiently, not is it the one I think humans should spend their life/mental power on. Especially when your estate grows and the amount of switches/devices multiplies.

So smarter ones of those(humans) started coming up with solutions. Enter the era of

## Analog devices and tailored solutions.

So lets formalize the problem a little bit: there's an electrical device which provides value to you, but only a subset of the time, that needs management. A light bulb in your (home) office serves a great example. You'd generally want it "on" only when you're at work and it's dark in the office. So how do you solve this?
"Just turn it 'on' upon entry and turn it 'off' upon exit, you dummy!" - one would say and be right!

If there's a space you enter and exit often though, you might be ok switching "on" the light, but switching "off" is something entirely "else". A generic timer solves the problem. The problem with this solution is that generic timer has a fixed "time". So you can get a motion-detector with the timeout - problem solved! mmmm almost: what if you only want this to be on when it's "dark"? This motion sensor becomes much more expensive and search for it becomes much more involved...

At this point you have 95% of requirements done and you've gone through a small zoo of switches/sensors/solutions. the 5% still lingers...

## Enter smart home automation.
It started with bulbs and now it's pretty much in as many devices as you'd be interested in. Starting with [Z-Wave][5], [ZigBee][4] and lately [Thread][3] these devices cost a little extra, but add a great deal of versatility to your day-to-day. Also, since they operate on a separate band, using a separate gateway, that is, most likely, connected to your main net via a single wire, they do not interfere with you regular WiFi needs, which so many have grown so sensitive to. The greatest challenge becomes, instead of choosing devices - choosing the ecosystem.

Now, let's address the elephants in the room: Google(with google assistant) and Apple (with Siri home automation) and Amazon(with Alexa ecosystem). All of these are very powerful, but they require devices to work over regular WiFi, which, as mentioned above, is a non-starter. WiFi is heavy, power-hungry and unreliable in small devices. So you may still use some of their integration, just to be able to say "XYZ - switch off all the lights please", but somehow without the hassle of seeing all of your devices being regular citizens in your home WiFi(even if you've gone an extra mile of connecting all of them to a separate edge network). So you need a hub.

On the market there have been a couple, notably SmartThings and [Wink][2]. I've used Wink for quite a few years with great success. However since they switched to the paid model, I have really seen them make any of their infrastructure more reliable, not have I seen any updates to the app. In fact their user base have grown to inflict outages, that would go unfixed for days at a time.

This is when I switched to Aeotec(aka SmartThings with a brand new name). I was surprised not only by feature parity, but with the fact that Samsung platform allows for even more flexibility in configuring and using things, being at the same time completely free. For the first few months there haven't been any disruptions in the service as well. I kept my Wink router just in case things go horribly bad and I have to switch back, but since that time almost a year has passed and I'm no way looking to come back to Wink. I still occasionally get notifications from Wink, their services being down. Now that amuses me. The ratio of services outages between Wink and Samsung seems to be, anecdotally 10:1. And it's free.

It's also much smaller then Wink, which makes it way more portable. Android users might also enjoy a greater level of support/integrations. And switch isn't _that_ painful. Yes you'll need to reconnect all of your devices... but it's only upside from there.

Finally, if you're still putting away switching, hesitating on Wink improving or wondering if SmartThings would be same-or-worse - don't be. It's same-or better, and, as of today, it's also free.

So there you have it: buy a Z-Wave/Zigbee/Thread switch-> install-> get the Aeotec hub -> configure via the app -> bliss: 

* now you can tell your assistant to switch off the light.
* now the light would automatically switch off after 15 minutes
* but only if you're not in the room
* and also witch on when you enter
* but only when it's dark
* and only to 30% if it's a weekend
* and then increase to 77% if the desk lamp switches on.
* but go back to 30% if it switches off, but only on weekdays

yes you can build such complicated rules and not only for the switch... but, in the end, it's all some sort of a switch... :)

 [1]: https://aeotec.com/products/aeotec-smartthings-hub/
 [2]: https://www.wink.com/products/wink-hub/
 [3]: http://threadgroup.org
 [4]: https://csa-iot.org/all-solutions/zigbee/
 [5]: https://www.z-wave.com

