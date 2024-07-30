---
title: 'Backing up Unify router config without Remote Access'
author: Viktor Halitsyn
date: "2024-07-30"
categories:
  - code
tags:
  - unify
cover:
  image: /images/2024/unify2024.png
  responsiveImages: true
ShowReadingTime: true
comments: false
  
---

For those who decided 'Remote Access" feature of Uniyfy isn't worth the [security risks][1] associated with it, but still want a local backup of the config, I've found a decent solution. 

Sharing this here, since it's not readily searchable on the internet nor would AI give you a good answer if you ask about it. And if you run into this head-first, you'll quickly discover that 

* router doesn't have samba(cifs) mount support
* router doesn't have sftp/fuse mount

so your usual `rsync` options of backup fall apart. Luckily there's [RClone][2]. ([his article][3] I found basically does a great job comparing RClone VS BackiFy.

Except Backify has all the same issues as "Remote Access" IMHO.

So you do not have to use the cloud options, you can just do a clean samba or what-have-you to your NAS and then let NAS do the regular backup to cloud. 

Neat. Problem solved.

 [1]: https://arstechnica.com/security/2023/12/unifi-devices-broadcasted-private-video-to-other-users-accounts/
 [2]: https://rclone.org/smb/
 [3]: https://lazyadmin.nl/home-network/backup-unifi-controller-to-cloud/
