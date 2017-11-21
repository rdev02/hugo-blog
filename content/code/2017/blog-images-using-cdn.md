---
title: CDN image hosting for blog'
author: Viktor Halitsyn
date: 2017-11-18T21:37:00-07:00
type: post
categories:
  - tech
  - tools
tags:
  - cdn
  - hugo
  - aws
keywords:
  - cdn
  - hugo
  - aws
  - blog
  - image
  - iCloud
  - OneDrive
  - Cloudinary

---

It's rare that a blog has 0 images. Hosting them on the same box as the blog instance is the easiest solution, but it comes at a cost: disk space and page load speed. Unless you are a professional blogger, you are also not ready shell out many $$ for a CDN, so what options are there?

<!--more-->

# 1. Reference images on the web.
That works well in the beginning, but soon some of the urls start to die(server moved/content updated/etc). Thus this is not a great solution, especially when you have your custom content to share. Enter..

# 2. Use a cloud file storage service, e.g. [GDrive][1]/[OneDrive][2]/[iCloud][3]/[DropBox][4]/etc.

## GoogleDrive
I used to store my content on **GoogleDrive**. There is a way to get a [url][5] for your content, and it will most likely work. Until google decides to change the way it serves content or closes the API altogether. At the moment, the worst thing about this method is having to manually parse/create urls for your content. 

## OneDrive
**OneDrive** offers similar [functionality][6] without url manipulation. It works well and even offers some dynamic sizing options for your content, however I quickly ran into problems:

a) links are absurdly long. Here is a sample link, OneDrive generated for me: https://vfybkq.bn1303.livefilestore.com/y4mGqBlcQGSIsuw6OFD-MyiXqy5dq-7DqQCgrXbP8kko6a9YT1Fr8ggCKZtEAdbDOqQa1eLNBJs9ZrWtimhlGd9Kum54jI9nWD-XvxFXe82LAKj11EtvUynlpcA6w__DOYjkaDPllOF-Xrs5acIf_p5kh7S9c8p_kGs3qjzXwko2tXxqSDt1Oe9himcpdzjiofihRH_N4cHAKRqiHvGui1g?width=3072&height=1728&cropmode=none
gnarly, eh?

b) OneDrive screws up your PNGs. Filed a bug about that, so this should get fixed eventually, but for now PNGs are served with a black background. 

## iCloud and DropBox

do not really provide embed functionality. Or at least I was not able to find it readily. They provide you a way to share your content in a form of an album or a link (like this one 
https://www.dropbox.com/s/f0xeaz2asdwi31lv/IMG_1710.JPG?dl=0 ) to a specific photo, but that(link) would load the whole website and also, probably, sell some ads).

Out of all these gdrive was the only working, but also quite painful solution. I needed something better...

# 3. AWS S3.

Is a very simple service. You might argue it's not free, but I'd counter that it's not super expensive(cents per GB of storage/transfer/requests). However it is very simple and pleasant work with: you can use a variety of tools for getting your content into the bucket. Once there, sharing is as simple as ``https://s3-dc-name.amazonaws.com/bucket-name/resource_name.jpg``. That is it! You can just use that url and you content would load correctly and reasonably fast(depending on your requirements). All of that would cost you cents a month if your site is not content heavy.

Imo, this should satisfy most developers. If you want to go further,

# 4. AWS CloudFront, Cloudinary and other CDN's.

AWS is great in a way it is simple, well-priced and reliable. However it might not be the best solution for you: if AWS is just something you do not feel good about, [Cloudinary][8] is a great place to start. They have a free tier that should satisfy a medium blogger and, most importantly, they have an absolutely lovely [url api][7] for content transformations.


[1]: https://drive.google.com
[2]: https://onedrive.live.com
[3]: https://www.icloud.com/
[4]: https://www.dropbox.com/
[5]: https://productforums.google.com/forum/#!topic/drive/EBjKgPLGSMc
[6]: https://support.office.com/en-us/article/Embed-files-directly-into-your-website-or-blog-ed07dd52-8bdb-431d-96a5-cbe8a80b7418
[7]: https://cloudinary.com/documentation/image_transformations
[8]: https://cloudinary.com/pricing