---
title: Seagate BlackArmor NAS 400.
author: Viktor Halitsyn
date: 2013-02-05T08:20:00+00:00
cover:
  image:  https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/BlackArmor400/ext-nas-blackarmor-nas-400-overview-2-316x268.png
excerpt: |
   So I decided that I'm sick and tired of plugging in and out usb drives. Be it my tiny 16GB daily thumb drive, or store-it-all 2TB external seagate - this is a pain... And to make sure everything is stored you have to "detach/remove" them safely all the time. And than eventually you need more storage(or will need) and expanding this kind of solution means more devices OR significantly more money. This is long and tedious so for quick and impatient there's a summary at the end.
categories:
  - tech
  - gadgets
disqusIdentifier: 5154575809
tags:
  - nas
gallery:
  - https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/BlackArmor400/SeageBaConsole.jpg
  - https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/02/004592-1-1.jpg
  - https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/02/004592-1-1.jpg
  - https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/02/004592-1-1-550x262.jpg
  - https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/02/004592-1-1-230x109.jpg
  - https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/02/004592-1-1-350x166.jpg
  - https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/02/004592-1-1-480x227.jpg

---
<div dir="ltr" style="text-align: left;">
      So I decided that I&#8217;m sick and tired of plugging in and out usb drives. Be it my tiny 16GB <i>daily</i> thumb drive, or <i>store-it-all</i> 2TB external seagate &#8211; this is a pain&#8230; And to make sure everything is stored you have to &#8220;detach/remove&#8221; them safely all the time. And than eventually you need more storage(or will need) and expanding this kind of solution means more devices OR significantly more money. This is long and tedious so for quick and impatient <b>there&#8217;s a summary</b> <b>at the end</b>.<br /> Believe it or not <i>more money</i> is inevitable so I decided to try and reduce the number of devices &#8211; pile everything up in one place &#8211; shall it stay safe and robust and accessible at all times. NAS. I had very few requirements:</p> 
  
  <ul style="text-align: left;">
    <li>
      at least 4 bays to be able to play with RAID 5/6/10.
    </li>
    <li>
      3.5 drive interface to keep them cheap
    </li>
    <li>
      &#8220;quiet&#8221; feature &#8211; at this would stay in my room.
    </li>
    <li>
      robust and reliable.
    </li>
  </ul>
  
  <div style="clear: both; text-align: center;background-color: white;">
 {{<figure classes="fancybox left" src="https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/BlackArmor400/ext-nas-blackarmor-nas-400-overview-2-316x268.png" title="BlackArmor400" >}}

    </div>
  
  <p>
    I had a couple of Seagate drives before and overall my experience with them was good. Nowadays you can find complaints about pretty much any manufacturer and any product so I did not really pay attention to what <i>others</i> said &#8211;  was looking for my set of requirements for the <i>right</i> price. And there it <a href="http://www.seagate.com/external-hard-drives/network-storage/business/blackarmor-nas-400/" target="_blank">shines</a> &#8211; perfect solution for all my needs and even a little bit more! Remote management, encryption, DDNS, configurable backups, all-you-can-imagine media sharing, internal downloader and more &#8211; sweet!<br /> So I went to Amazon and ordered one. It was just before new years so I received my order in a month &#8211; but that was not urgent: storage is not smth. that one should make haste with IMO.
  </p>
  
  <p>
    <b>Packaging and build quality.</b><br /> I was actually impressed &#8211; the thing was of manageable size, pretty compact and fitted into my nightstand lower<b> </b>deck. Loading this thing up with drives was super easy &#8211; it had a smart clip drive holding <i>system.</i> No installation &#8211; you can install some <i>discovery</i> program from CD but really &#8211; you do not need it. The only thing it does &#8211; it finds the IP of your NAS and forwards you to the browser.<br /> Thus overall I was pleased with build quality: yes it was not top notch aluminumnickel but mostly plastic. Yes it was a bit rough on the edges. Yes the green <i>screen</i> was smth both enjoying and amusing, but for the price it was absolutely great and matched my needs in full. I was not going to use this in hazardous environments and I do not have little kids so that I would really worry about the build and plastic. The chip and software is what mattered&#8230;.
  </p>
  
  <p>
    <b>UI and software</b>
  </p>
</div>

  {{< figure src="https://s3-us-west-2.amazonaws.com/vnomad-public/blog_infra/2013/BlackArmor400/SeageBaConsole.jpg" title="UI Console look" >}}
 More images below in the gallery.
<div dir="ltr" style="text-align: left;">

  <p>
    UI was slow. As a developer I&#8217;m used to wait times. I&#8217;m patient. But even for me it was tad slow. It was intuitive though. I quickly found my way around without the manual but then the limitations kicked in<br /> &#8211; you have have a usb stick plugged in to the first usb slot(and only 1st, which front btw) if you want encryption<br /> &#8211; you have to have entire volume encrypted<br /> &#8211; you have to wait all surface scan and zero-out if you change anything about the volumes(and it lasts about 8 hours on my 2TB drives)<br /> &#8211; you cannot have a read only share(bummer!)<br /> &#8211; you have to do 1 thing at a time or else everything you configured before may get messed up and only hard reset followed by total zero-outcheck of drives(8 hours again) will save you<br /> &#8211; and probably many other I did not discover due to I did not plan to use that functionality at all.<br /> BUT I went through all this, patiently, believing that once I get this linux set up and working &#8211; it&#8217;ll be rock solid and serve me well for many years. That is how it is usually done with linux &#8211; you get it for cheep, but you pay with your time. Your configuration is a pain, but after all it worx (period)
  </p>
  
  <p>
    <b>The worx</b>.<br /> So I wanted to make sure that I can rely on this thing once I configured it. So I wrote a little program, that would generate a set of files, get their md5&#8217;s, measure speed and have some brain to verify that files later. After all, the only way to understand if the drive would work for you &#8211; is to fill it up with data and see how it performs on parallel reads/writes. I&#8217;ll post the code soon to github in case anyone would ever need it. I will.<br /> I started it up on 2 computers: one writing to encrypted partition, other &#8211; to public one(both raid 10) and a few days later I had my NAS populated with dummy, but recorded data. Thus:
  </p>
  
  <ul style="text-align: left;">
    <li>
      write speed turned out to be much slower for the encrypted volume than for the public one &#8211; around 2-5 Mb/s VS 5-7 for the unencrypted.
    </li>
    <li>
      read speed was good for both 10Mb/s for encrypted and 15-20 for unencrypted volume.
    </li>
  </ul>
  
  <p>
    That was more than satisfying for me. I do not really need fast write, and that yields enough speed for one HD movie stream. Next were the problems tests that I was planning to execute &#8211; like remove a drive and see how NAS tackles it. But I did not have to do this problems arrived by themselves&#8230;
  </p>
  
  <p>
    <b>The problems.</b><br /> Another morning I woke up and found out that NAS reports one drive to be failed. No big deal &#8211; take it away and put a fresh new one. But I wanted to test out the speed and file integrity before doing this. So I launched my program again in test mode and an hour later my second drive was reported to be failing. Now I was in doubt, because the filesystem was still available. 2 hard drives from one branch of raid 10 fail and FS worx&#8230;how? Well. I restarted the NAS and drives showed up as <i>good</i> but both volumes showed <i>degraded. </i>Then 2 drives rendered failed and public volume remained degraded. Let me remind you that both volumes were based on same drives with same raid setting. Next the unpredictable happened &#8211; all data from public volume <i>disappeared</i>. Volume was mountable, however displaying 90% full and empty at the same time. So I decided to take both of them and inspect in the external enclosure. The drives indeed turned out to have bad sectors on them. Fedora immediately told me that I want to replace those drives. BUT they were readable and fine. a couple of bad sectors could have been easily remapped afaik&#8230;.<br /> So I decided to give this NAS the last try to restore the disks with the spare drive I bought just for this purpose. Actually I decided to give NAS a chance to do what it was essentially bought for &#8211; tackle the hardware problem. But after I inserted the disks back(nothing was done to them &#8211; I just ran disk utils and gparted, I did not repair or remap anything. and yes &#8211; I labeled all drives before putting them into nas). &#8211; NAS told me that &#8220;Problem&#8230;.Data is recoverable. Please contact support&#8221;. and pay money, probably&#8230; That was it for me &#8211; filed a return on amazon and the next morning it was gone.
  </p>
  
  <p>
    <b>SUMMARY</b><br /> <b>pros: </b><br /> + relatively good pricing(~200$)<br /> + nice form factor(small)<br /> + nice set of standard features out of the box(ddns, media service, downloader, raids)<br /> + enough speed for small office/home experiences.<br /> <b>cons:</b><br /> &#8211; not reliable(easy to corrupt internal FS, unable to restore/maintain data)<br /> &#8211; clunky, slow and strange software(not only UI). IMO<br /> &#8211; apparently has problems with concurrent usage. AFAIU<br /> &#8211; not able to address basic hdd hardware problems(remap or even test for bad sectors &#8211; only does a S.M.A.R.T test and even that unreliably).<br /> &#8211; <i>virtual</i> support(The <a href="http://forums.seagate.com/" target="_blank">forum </a>I visited looks like gives answers only to <i>satisfactory</i> questions or those you can find out by yourself.)
  </p>
  
  <p>
    <b>Next stop: </b><a href="http://www.synology.com/products/product.php?product_name=DS413j&lang=us" target="_blank">Synology</a>. To be continued&#8230;
  </p>
  
  <p>
    *all pictures are referenced from seagate.com
  </p>
  
  <p>
     
  </p>
</div>