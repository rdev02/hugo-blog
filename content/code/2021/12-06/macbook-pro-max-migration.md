---
title: 'x64 Macbook Pro -> Apple Silicon Macbook Pro Max'
author: Viktor Halitsyn
date: 2021-12-06T22:07:00+00:00
type: post
categories:
  - tech
tags:
  - hardware
thumbnailImage: /images/2021-12-12/m1PostLogo.png
---

Few things motivate me more, than exciting new hardware and music. My last post has been over a year ago, and that was when Synology released new exciting NAS options. Side-tracking a bit, 920+ turned out to be well worth it's money: a year later it's still as performant as day 1, though I did have to [disable the RW cache][1] on it :). 

However today the new [M1 Pro Max][2] takes spotlight. Frankly, I was quite thrilled to order it: brand new arm platform, refined for ~1 year since  M1 launch. What is it going to be like? I purposefully haven't read much about compatibility and perf tests so that my experience was be kept pristine. And thus, after watching the keynote and learning that my beloved 16 inch form factor is available without the controversial touch bar and a proper row of sturdy buttons instead - i took the plunge and ordered it:

**MacBook Pro (14-inch, 2021)**
* Chip: Apple M1 Max
* Memory 32Gb
* Disk 1Tb

I wanted to go 16Gb RAM, but I ended up with 32 and here's why: `Max` with 24 core GPU cannot run 16Gb. Website just won't let you order that config and it's quite subtle: you toggle that radio button on the chip, suddenly you are another $600 in. I was so surprised that I filed a bug for them. As usual: by design :D.
With the purchase of home server, the amount onboard RAM became less important to me. Welp - more RAM is always better. I rarely get baseline models: they tend to be just a "sneak peak" of what the package was intended to be, so I went for it. Just a month and a few days later it arrived.

**First impressions**

It's thick(that's what she said). It's also heavy: heavier then my former 14" MBP. I really liked the charge cable, even though frankly, with my dock station and urge to move 100% usb-c everywhere I don't see myself using that(charge cable) very often. If only they provided a usb-c of the same quality: that'd be awesome.

*The screen* is way better and so far, doing regular office/installation/dev work I did not notice much discomfort with the brow: most tools I use either support it out of the box, or added support recently (like JebBrains Idea, which I use a lot.)

*The keyboard* is very pleasant. I'm not a keyboard geek, and for me, the surface keyboard is still the one I like to type on the most(out of the stock keyboards one could buy off the shelf in reasonable time). On 14" model touchpad and keyboard size work perfectly, so that you almost never hit on touchpad accidentally. When using touchpad - it has quite a bit of surface to make mousing comfortable. it's rare occasion for me to reach out for my MX Master...

*The noise* (aka the fans)... is absent(stay silent)! Probably **the** biggest win since the last x64 intel processors. There's virtually nothing in a regular(java/go) development flow that would make the fans scream. Which would easily launch my previous fans full-throttle. Virtualization doesn't do it. Back-to-back installations do not do it. I haven't really processed much video on it yet(still have to unpack and test out my new GoPro10).
~ 1mo into owning this machine I'm yet to hear it turn on fans... Or maybe it did, I just did not hear it :) Either way - works for me.

*The battery* is another big win. After a couple years have passed I had to replace my mbp battery. Even after having done that, (battery) performance wasn't that great. I could do 2-3 hours of development, but I really had to watch my battery. I could only hope to survive a day if it was an office mail-and-chat day (no videoconferencing).
The story has completely changed with this little helper: I can do a full day again(yay!). I can also throw in a few video calls and I do not need to strategically plan my position being close to the charger at all times. Way to go Apple!

**Software support**

M1 is ARM and that puts quite a bit of hesitation in people's minds. I'm no exception. This time I decided to go "clean install" such that none of the x64 "junk" creeps into the new platform(there turned out to be way more then just "x64 stuff"...) and I get native versions instead, when available. Another hesitation was around Rosetta compatibility: would I need to do anything extra? How smooth is it?

My worries were greatly exaggerated. It might have well been an issue at M1 launch, but it's definitely not an issue anymore. Most things are installable via Brew casks these days and you'll get an arm version if one is available or an x64 version if not. Both would launch just like native. I haven't noticed any lag or perf issue for the apps I use. Here is quick rundown of notes I've been taking while installing:

- Things installed via brew: Tunnelblick, Skype, VLC, GPG suite, docker(client), jdk, Parallels, Steam...
- Things that did not work:
  - Plex player(can be replaced with web version)
  - docker-machine(it 'kinda works')
  - Tor(installed but did not work)
  - parallels x64 vm(duh!, but the move to 11 arm was almost painless, more on that below)
- Things that required a separate install
  - Office365 (thanks, Microsoft!)
  - Paragon FS tools (grab the 50% off deal from you account!)
- Things I moved manually and they worked with no issues
  - TurboTax 2008
  - anything else I moved? I think I moved something else... anyway: it just worked.
- Games... That is a bit more sad, but still quite a lot of things work via steam:
  - Doom
  - FTL / IntoTheBreach
  - MadMax
  - BattleTech
  - Worms :)
  - Civilization 5+
- and these do not
  - Portal
  - Counter Strike 

On the games note, one game I keep playing since "forever" is [HMMSoD][3], which has now become [HoTA][4](combining HD mod with some nice improvements). Interestingly, after installing fresh win 11 arm via Parallels HoTA ran there smoothly in [compatibility mode][5]. Crysis averted!
No such luck with Age Of Mythology. The game installed and ran, however custom Apple graphics hardware + Parallels emulation doesn't let it happen. I have a strong feeling the game might be fooled/forced to still run(HMMSoD is older and still runs), but I just can't make myself spend that much time on this, minding my windows rig is just 1 network hop away...

**A note on time machine**
All the simplicity and greatness of Apple time machine shines when one uses it as intended: to restore a mac or to restore files from within a mac that is connected to said time machine(backup). Things get a bit more cumbersome when one just needs to pull a fricken file off the encrypted backup...
See you can't just open a source and explore a-la-carte: full restore or nothing it is. So I opted to just manually backup my user directory to an external drive... Sound reasonable, right? kinda wrong :)
Doing a simple `cp -r` of a 500Gb driver(filled 75%-ish) resulted in 1.2Tb of "copying" on the NAS... at which point I stopped the while thing and cherry picked stuff I needed manually. Worst case - can restore into the previous one, draw files and restore back into the current one. But now that the disk is 1Tb this just takes quite a bit of time and really reminds me of 

{{< image classes="fancybox center" src="/images/2021-12-12/moveIt.gif">}} 

I just need a few files, okay? Why can't I has easily via some TM UX? Is that so much to ask?

**In summary**

I'm very happy with my purchase: better screen and power with very little migration issues. Let's see how this works out over time. I'll try to post any interesting migration issues as those happen. Super curious to test out video performance...


 [1]: https://www.reddit.com/r/synology/comments/pth8nf/ds920_lost_power_ssd_rw_cache_now_empty_how/
 [2]: https://www.apple.com/shop/buy-mac/macbook-pro/14-inch-space-gray-10-core-cpu-16-core-gpu-1tb#
 [3]: https://www.imdb.com/title/tt1458918/
 [4]: https://h3hota.com/en/documentation
 [5]: https://blog.bullgare.com/2021/09/heroes-3-on-macos-with-m1-apple-silicon/