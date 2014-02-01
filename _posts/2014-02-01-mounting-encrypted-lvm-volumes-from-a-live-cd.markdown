---
layout: post
published: true
title: Mounting Encrypted lvm Volumes From a Live CD 
category: news
---

I once again fried my system and had to recover some files from an
encrypted filesystem via a Live CD.  It's a frustrating experience.  I
tried both Debian Wheezy and Ubuntu 13.10.

First, after you boot it will show an encrypted filesystem icon.  If
you click on that the system will prompt for your password.  After you
enter your password, it will complain that the partition is invalid
and can't be mounted.

I've seen this before.  In the past I would simply run `sudo apt-get
install lvm2` and the volumes would appear.

This time around, I was having no such luck.  After some surfing, I
found this [Ubuntu Forums
thread](http://ubuntuforums.org/showthread.php?t=940904).

It basically worked.  Here's the exact sequence I followed on my
system:

    sudo apt-get install cryptsetup
    sudo modprobe dm-crypt
    sudo apt-get install lvm2
    sudo modprobe dm-mod
    sudo vgscan # this outputs the volume name
    sudo vgchange -a y volume_name_from_above

After the last step, my encrypted volumes magically appeared on my
desktop.

Hope this helps some other poor soul,

Grant