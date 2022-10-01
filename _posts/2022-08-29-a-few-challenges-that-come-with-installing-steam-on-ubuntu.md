---
layout: post
title:  "A few challenges that come with installing Steam on Ubuntu"
date: 2022-08-29 12:29:13 +0530
categories: miscellaneous
description: >-
  Installing Steam on Ubuntu, and resolving all issues and errors that come with it.
tags: [jekyll-blog, miscellaneous, steam, ubuntu, steam-install]
---

<style type='text/css'>#markdown-toc::before{content:'Table of Contents';font-weight:700}#markdown-toc{border:3px solid #aaa;padding:1.5em;margin-left:0;display:inline-block}</style>

* TOC
{:toc}

Again as on a typical Sunday, being bored enough to want something to do, but not enough to start some new project; there should be something that can be a good fit for this mood, right? Why not try out some games? On Steam?

I've heard that Steam is stable enough to run Windows games on Linux Machines using `Proton` Let's try out some games, shall we?

The thing is, I'm not an avid gamer, or any sort of gamer in that sense. I don't have the patience to grind through games in the first place, so first I need to find out some games that I actually want to try out.

I had some criteria for the games I wanted to try out:

1. Casual Game
2. Nice Art
3. Free to Play

Thus began my search on YouTube. I watched a few videos, and decided on some games to try out.

The shortlisted YouTube videos are below:

* [Top 5 Free Casual Games on Steam You DON'T Know About](https://www.youtube.com/watch?v=a8vR_AaXl8I)
* [Relaxing Games 2021](https://www.youtube.com/watch?v=NZ4ffJHzEXU)
* [Top 10 Best Indie Games of 2021](https://www.youtube.com/watch?v=u-ronEQXB08)
* [Best Free Relaxing Games on Steam](https://www.youtube.com/watch?v=C2aSbIZQKt4)

The list of games that I eventually tried out are:
![Casual Games I recently tried out on Steam](/assets/img/29-08-2022-casual-games-list-trying-on-steam-.png)

---

## Installing and setting up Steam

My laptop details:
> **Operating System:** Kubuntu 22.04 <br>
> **KDE Plasma Version:** 5.24.4 <br>
> **KDE Frameworks Version:** 5.92.0 <br>
> **Qt Version:** 5.15.3 <br>
> **Kernel Version:** 5.15.0-46-generic (64-bit) <br>
> **Graphics Platform:** X11 <br><br>
> **Processors:** 8 × Intel® Core™ i5-8250U CPU @ 1.60GHz <br>
> **Memory:** 7.5 GiB of RAM <br>
> **Graphics Processor:** Intel® UHD Graphics 620 **|** Nvidia GEFORCE MX150

<br>Installing `steam` was a straight forward step using the `steam-installer`

```bash
sudo apt install steam-installer
```

Once that is done, the next step is to run the `steam-installer` and it will go through the required installation process.

Once all the setup, followed by login is done, then it's time to install some games to try out.

The first game I wanted to try was _Island Saver._ For that, I setup the Steam Library to my HD (1TB HD, mounted @ `/dev/sdb1` ) The game got installed, along with all other dependencies like `proton-experimental` and _steam-linux-runtime_ `soldier`

But, starting the game did nothing. I rebooted my system but the same issue persisted. So, I started steam from the terminal to get a view of all logs, messages, and errors to the stdout.

**First, I saw errors:**

```text
Missing decoder: MPEG-4 AAC (audio/mpeg, mpegversion=(int)4, framed=(boolean)true, stream-format=(string)raw, level=(string)2, base-profile=(string)lc, profile=(string)lc, codec_data=(buffer)1190, rate=(int)48000, channels=(int)2)
```

This was due to some _plugin missing in **GStreamer**_ After some research, the only solution proposed was to install `ubuntu-restricted-extras`

```bash
sudo apt install ubuntu-restricted-extras
```

which, did not resolve the errors at the end, but all the games still worked, so this error resolution was kept on a hiatus.

**Then, I saw errors:**

```text
OSError: [Errno 22] Invalid argument: '../drive_c'
```

which upon some google search revealed that this was an issue due to my HD being in NTFS format. Apparently, this is a common and well [documented issue in Proton,](https://github.com/ValveSoftware/Proton/issues/5168) which arises due to _Wine_ using `:` (colon) in the path.

* Tried creating symlink from my HD to `~/home/user/SteamLibrary` but that did not work:
  * First, it did not register the symlink in the Steam App so that I could select the library
  * Secondly, I created the actual folder, and symlinked the contents inside `SteamLibrary` but that gave the same error
* The only thing left to do was to delete the above, and create the library on the SSD (formatted to `ext4` ) which worked

**Finally got the game working.**

---

## Resolving issue with Nvidia drivers

Now that the game was working, I wanted to switch the GPU profile from `on-demand` to `nvidia` so that I could actually use the Graphics Card.

> **THAT WAS A BIG MISTAKE**

I rebooted my system, and it got stuck on boot. I repeatedly restarted doing different things but it would get stuck on boot.

A lot of frustration, some deep contemplation about just trashing everything and reinstalling everything, and loads of Google searches later, I came across an instruction to bring up the _terminal_ post that boot screen using `C+f2,f3,f4` in sequence.

Once in the terminal, login, and change the profile to `on-demand`

```bash
sudo prime-select query
# output: nvidia
sudo prime-select on-demand
```

Then, after the reboot, the system started back. This meant that the _NVIDIA drivers_ installed were not the correct ones.

So, let's remove the current _NVIDIA drivers_ and installed fresh ones.

```bash
# remove the current nvidia drivers
sudo apt remove --purge *nvidia*
# reboot
reboot
```

The system will reboot with the default `nouveau` drivers which will work with the _Intel Graphics 620_ of the laptop. The next step was to install the default drivers from the _**Driver Manager**_ in KDE.

![Installing nvidia drivers from Additional Drivers](/assets/gif/setting-additional-drivers-install-nvidia-515.gif)

Then, changing the `prime-select` to `nvidia` and reboot.

---

### Finally, the games started working

However, I still had doubts whether the GPU was actually being used to render or not.

To get a good view of the GPU usage, I [installed _nvtop_](https://github.com/Syllo/nvtop) which is similar to _htop_ but for GPUs.
