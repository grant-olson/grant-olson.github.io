---
layout: post
published: true
title: Using MiniCom with FlashForth on OSX
category: news
---

I've been spending some of my free time lately playing with Arduinos and
Raspberry Pis. They are dirt cheap, have GPIO port exposed, and there are
plenty of peripheral devices available from China for as low as 1 or 2
dollars. Controlling the hardware is different enough from my day job it
makes a great engineering hobby.

The Arduino IDE is nice
if you're trying to bring up some hardware quickly, but I was a little annoyed
at the way all the actual hardware access was hidden behind library code that
you don't see in day-to-day development.   My adventures took me to [FlashForth](http://flashforth.com/), an implementation
of Forth targeted for the microprocessors that run the Arduinos. Forth has
always been on my languages-to-learn list. It's supposed to be a high-level
language that can be implemented in a small footprint (as small as 8K!), has interactivity,
and gives you direct access to the bare metal. In the past I'd just be using it
in a sandbox without touching hardware, but now I've found a good excuse to
play around with it on these devices with only 32k of memory and utilize the
bare metal access.

Things had been going well as I worked on tutorials and got the basics of
Forth down, but came to a screeching halt when I tried to start developing a
real project (an sd card driver) and was writing my code in files and
sending various code blocks to the device as fast as my computer would let me.
FlashForth would just start printing a bunch of `|||||` and wouldn't receive
the input. This problem is documented on the homepage:

> Normally communication with the PC and writing to flash works very reliably, but...
> 
> If to you see a vertical bar *|* output from FlashForth, it means that the
> UART RX interrupt buffer has overflowed.
> 
> It is usually caused by the PC reacting slowly on XOFF.
> `setserial /dev/ttyS0 low_latency` improves the situation on Linux.
>
> On Windows, disabling the UART buffers improves the situation.
> Another alternative is to use TeraTerm with an intercharacter delay of a few milliseconds.

Unfortunately the fixes were for Windows and Linux, but nothing for OSX. The `setserial`
command isn't included on OSX. I eventually decided I needed to find a terminal program
that ran on OSX and allowed you to set the above listed *intercharacter delay of a few milliseconds*.
This proved easier said than done. After spending time trying a plethora of programs, both
Open Source and commercial demos, I finally found out that **minicom** could do what I needed,
and was available via brew.

Configuration is a little tricky though. There were a few settings I needed to change to make
things work. And unfortunately the pause-between-characters setting doesn't get saved in
configuration, so I need to set that up every time I fire up the app. But once I'm up
and running it works well. Here are the two phases of setup:

## Initial Configuration

1.  Install minicom: `brew install minicom`.
1.  Start minicom: `minicom -b 38400 -d /dev/cu.YOURDEVICE`
1.  `ESC-Z` brings up menu.
    1. `O` for `cOnfigure Minicom`.
    1. Down arrow to `Serial port setup` hit `ENTER`.
        1. `E` to set baud rate.
        1. Optionally `A` to set serial device, but mine changes enough I use the `-d` command line flag.
        1. `ESC` to go up a menu level.
    1. Down arrow to `Screen and keyboard` hit `ENTER`.
        1. `P` to set `Add linefeed` to `No` so you don't double space.
        1. `R` to turn `Line Wrap` on so a command like `words` doesn't run off the page.
        1. `ESC` to go up a menu level.
    1. Down arrow to `Save setup as dfl` hit `ENTER`.

At this point all of the savable settings are stored in your config for later. We still haven't solved
the original problem of adding a delay though. This will need to be done every time at minicom startup.

## Add intercharacter delay

1.  `ESC-Z` brings up menu.
1. `T` for `Terminal Settings`.
1. `F` for `Character tx delay (ms)`.
1. I went with `10` ms for best results on my system. Lower numbers may work for you.
1. `ESC` to leave menu and return to main screen.

At this point you should be able to safely paste large chunks of code for processing by the FlashForth
interpreter.

Have Fun!

Now I just need to work on direct Emacs integration instead of using `Ctrl-C` `Ctrl-V`. If anyone has
tips let me know.
