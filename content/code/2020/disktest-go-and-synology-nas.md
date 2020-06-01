---
title: 'DiskTest in go and Synology NAS'
author: Viktor Halitsyn
date: 2020-05-31T19:36:00+00:00
type: post
categories:
  - tech
  - code
tags:
  - nas
  - go
thumbnailImage: https://vnomad-public.s3-us-west-2.amazonaws.com/blog_infra/2020/DS918Plus.png

---

Back in 2013 I got myself a [Synology DS413j][1], which has proven to be a reliable companion for me though these 7 years. I used it for file backup, media streaming and as a TimeMachine destination pretty successfully. Any such station needs good drives to provide value, and drives, as many of us know, are pretty unreliable. In 7 years I replaced about 6: 2 were bad right after arrival, and another 4 went bad as the years went by. Only 2 original drives, purchased back in 2013, are still in service.

To suss out bad drives sooner, rather then later, I used a small java tool I wrote. The idea behind the tool was simple:

write -> record -> read -> verify

Defective drives would actually fail pretty quickly on the first full write and read. Or they would exhibit bad sectors after the sequence: those were returned by warranty very easily and expediently.

The tool was written quickly, held many unnecessary things in memory, ran sequentially, but, most importantly, it worked. That was till  the drive size grew and suddenly my little tool started to OOM closer to the end of the verification phase. Of course I should have profiled the tool and improved it... But I do that(profiling and improving) as my day job, so instead I decided I can practice a language way better suited for things like that

**Go**

So I simply rewrote my little tool in go, and this time, made it open source. Welcome [DiskTest][2]. Now you can easily stress-test you drive like this:

```
disktest --size="10TB" /mount/driveZ
```
It should would use minimal RAM(about 100MB, most of which would be dedicated to 20MB IO buffers) and run up to the limit of your drive's IO.

While at it also came across a few useful commands for dealing with drives/partitions for linux. I'll just list them here for posterity:

Checking drives and any partitions present
```
lsblk
fdisk -l
```
making partitions
```
mkfs.XXXX /dev/sda
```
where XXXX is the partition type, i.e. xfs, ext4, etc.

Checking for bad blocks:
```
sudo e2fsck -c -v /dev/sda1
badblocks -b 4096 -s -v /dev/sda1
```
The first one invokes the second one along other checks. However the first one doesn't work with `xfs`, which may be a deal breaker.

Since your drive is probably S.M.A.R.T, using bablocks might yield nothing, since the drive firmware would map those away transparently for any OS process. To know for sure, you'd want to check S.M.A.R.T info on your drive. That is usually quite simple with Synology or any GUI shell, but if you're not there just yet and would like to know - these things were of help for me:
```
 apt install smartmontools --no-install-recommends
 smartctl --all /dev/sda
```
IF the output says that the test hasn't been run, you probably want to run one :). At least a short one:
```
smartctl -t short /dev/sda
smartctl -t long /dev/sda
```

So I got the new [DS918+][3] to test it and the drives :). While testing drives for my new setup I experimented with a couple ways of loading the drive IO, monitoring the overall throughput via 
```
iotop -o
```
My general finding is that IO-wise running 10 threads in parallel against the drive is just as fast as running 2, just eats more RAM for the buffers.


Another interesting finding was disk IO speed at saturation points: closer to the disk being 100% full disk write speed deteriorated greatly: went down to single digit `M/s`. I did not take the time to understand if different FS would handle this differently, but this little observation re-iterated the rule of **keeping at least 15% of the drive free at all times**.

Last but not least, before selling/trashing your old drives you might want to consider 
```
shred -vf /dev/sda
```

Synology, again, has the same tool avoilable via GUI, however you won't be able to run it on the last drive(where Synology is intialized).

 [1]: /code/synology-ds413j-nas/
 [2]: https://github.com/rdev02/disktest
 [3]: https://www.synology.com/en-global/products/DS918+