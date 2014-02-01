---
layout: post
published: true
title: Setting up an OpenPGP smartcard and IronKey on Debian Wheezy
category: news
---

Setting up an OpenPGP smartcard and IronKey on Debian Wheezy
============================================================

My computer just died.  I threw the hard drive into another computer.
Everything looked good until it tried to fire up X and then I just got
a blank screen.  You know what that means.  Time to reinstall the OS.
There were a few gotchas that I thought I'd document here.

Live CD doesn't mount encrypted partitions
------------------------------------------

I run full disk encryption.  After my computer died I wanted to grab a
few files and backup the most  before re-installing the os.  I grabbed
the Debian Live DVD image with xfce.

Everything booted.  I clicked on my encrypted partition.  I was
prompted for a password.  The password was accepted.  But then the GUI
complained that it couldn't mount the filesystem.

After some trial-and-error, I learned that I needed to install lvm2:

    sudo apt-get install lvm2

Then I was able to access my encrypted partitions and get the
backup files that I needed.

OpenPGP smartcard
-----------------

After that I reinstalled the OS and all my favorite packages.  Gnupg2,
enigmail, thunderbird, keepassx, etc.  But after that my smartcard
wouldn't work.  I run into this problem every time I reinstall my OS!

But after installing gnupg2, I still couldn't use the smartcard.
This happens to me every time I reinstall Debian.  One long-standing
issue is that scdaemon, the driver for the smartcard isn't installed
unless you install the gpgsm package:

    apt-get install gpgsm

I've done that before.  But I still couldn't use the card unless I was
root.  I also needed to install lib-ccid and pcscd:

    apt-get install lib-ccid pcscd

After that I was good.

On this install I'm just running xfce.  In the past I've had problems
with gnome taking over the smart card.  See my previous post on [Using
an OpenPGP Smartcard on Ubuntu
12.10](/news/2013/03/09/using-openpgp-smartcard-on-ubuntu-12-10.html)
if you're still having problems.

Getting IronKey working
-----------------------

I also have an IronKey, which is a handy USB drive that has hardware
encryption and (like an OpenPGP smartcard) will self-destruct if
someone tries to brute force it.

Normally I just use the software included on the drive to mount the
partition.  But lately I've run into problems where the program
cryptically doesn't run.  This is because the software is 32 bit and
I'm running a 64 bit install.

You'll need to enable multi-architecture installs for 32 bit software and
install the 32 bit software to get the IronKey working:

    sudo dpkg --add-architecture i386
    sudo apt-get update
    sudo apt-get install ia32-libs

After that the provided software should work.