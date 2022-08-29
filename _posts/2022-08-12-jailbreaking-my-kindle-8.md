---
layout: post
title:  "Jailbreaking my Kindle 8"
date: 2022-08-12 13:31:46 +0530
categories: miscellaneous
---

I suddenly noticed that my old Kindle (Kindle 8) was lying in the corner gathering dust. I picked it up, carelessly dusted it, and put it on my desk, where it lay for a couple of days.

However, today, like many days recently, I was feeling bored enough to not do anything. Even YouTube and Netflix wasn't cutting it, so I closed my laptop, picked up my iPad and was about to leave my desk for the chair, when my eyes fell upon the Kindle.

Hmmm. It's basically useless now, since I do most of my reading on the iPad. I don't have any other use for it either. Plus, the device was a slightly damaged due to my distinct lack of care on top of my rough handling. What to do with it? Let's jailbreak it first.

And, that's how the hunt for jailbreaking started. It took me a couple of minutes to get the search query right, and land on a [forum post](https://www.mobileread.com/forums/showthread.php?t=346037) detailing the step for jailbreaking my particular version of Kindle.

### Device Info

**Kindle (8th generation)** <br>
**Wi-Fi MAC Address:** XX:XX:XX:XX:XX:XX<br>
**Serial Number:** G000 K9xx xxxx xxxx<br>
**Network Capability:** Wi-Fi<br>
**Firmware Version:** Kindle 5.14.1 (3776430030)<br>
**Space Available:** x.xx GB free<br>

I followed the steps mentioned in the post, and it was a smooth sailing in the beginning. But, failure is part of the process. I got stuck in a step in the _Jailbreak_ part of the process. <br>
> Copy ${YOUR_DEVICE}-${YOUR_FW_VERSION}.zip to .demo/

The problem was simple: I did not know the correct name of my device. My search queries did not yield the correct result I was looking for, and the results that it yielded led me down the wrong path.

I tried to [find the name of my device using the serial number the list](https://www.howtogeek.com/733834/how-to-tell-what-kindle-model-you-have/#moka_anchor_how_to_tell_table) and that led me down to believe that my **_Kindle 8_** was called _Kindle Basic 8_ or **_KB8_** in short.<br>
However, this was not among the list of devices present inside the ZIP file. Bummer!

I tried to try with some other device firmwares, fully aware that doing so will brick my Kindle with no way back. But, there was no other use for the Kindle. So, I tried it anyways.

I tried using the [release dates of the various Kindle devices](https://en.wikipedia.org/wiki/Amazon_Kindle) to use an alternate device firmware instead. I tried with **_PW3_** and **_KOA1_** but it was not meant to be. I got errors and despite trying various methods to force install the firmware, I was met with a dead end. There was no way to install an alternate firmware. This turned out to be a blessing in disguise.

After wasting some more time trying out other steps, I finally gave up. Temporarily.

I **_factory reset_** my Kindle and left this for now.

Back after a break, I started searching for other solutions like downgrading and alternate ways to jailbreak. Among one of this search, which led me to a reddit post on jailbreaking about 5 years ago. Perusing through the comments section gave me the insight that resolved the issue. <br>
[The comment responsible.](https://www.reddit.com/r/kindle/comments/752ece/comment/do3gsi5/?utm_source=share&utm_medium=web2x&context=3)

> The "Kindle 8th gen" is called Kindle Touch 3, or KT3

This was what I was looking for. I had no idea that the **_Kindle 8_** was also called **_KT3_** I had wrongly assumed that _KT_ stood for _Kindle Touch_ which was a different device from the basic Kindle.

The files for **_KT3_** was there, and the rest of the process was a breeze. <br>
[Forum post on the instructions](https://www.mobileread.com/forums/showthread.php?t=346037)

Now that the _jailbreak_ is successful, I will check out the various possibilities this offers.
