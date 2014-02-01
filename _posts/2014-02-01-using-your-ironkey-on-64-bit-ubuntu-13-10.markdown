---
layout: post
published: true
title: Using Your IronKey on 64-bit Ubuntu 13.10
category: news
---

The linux IronKey executable is 32 bit, so you can't unlock the
IronKey on 64 bit linux.  The traditional fix is to install
`ia32-libs`:

    sudo dpkg --add-architecture i386
    sudo apt-get update
    sudo apt-get install ia32-libs

This no longer works.  The internet tells us that this meta-package
was removed so that you don't install a bunch of garbage, and you
should just install the specific packages needed for your app.

Unfortunately there's no way to tell what packages are required.  The
internet says you should just run `ldd filename`.  This produces an
error on the ironkey executable.

After some trial and error, I found out you need the 32-bit gcc
libraries:

    sudo apt-get install lib32gcc1

After this, I was able to mount my IronKey.

I don't *think* that I needed to add the i386 architecture for this
command to work, but I'm not about to re-install my OS to test that.
So if the above command doesn't find a package, perhaps you should try
adding the i386 archtecture and updating apt.

Hope this helps another poor soul out there,

Grant