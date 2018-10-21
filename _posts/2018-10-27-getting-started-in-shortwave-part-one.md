---
layout: post
published: false
title: Getting Started in Shortwave and Amateur Radio Listening (Part One)
category: news
---

## *or*

## Everything I Learned in My First Month of the Hobby

I'm always looking for a new hobby, and got interested in listening to
shortwave radio recently. I was originally interested in listening to
broadcast stations from other countries, and quickly discovered a world of
amateur radio with a bewildering array of both voice and digital transmission
formats. There was a lot of information floating around on the
airwaves, but it was very difficult for me to decipher.

To make matters worse, I could find plenty of blog posts here and
there, but many of them assumed you had extensive knowledge of ham
radio and covered something very specific. I had trouble finding a
cohesive gentle introduction guide to the art of surfing the radio
spectrum.

This is an attempt to fill in the blanks and get someone like me, who
has zero background in this, up to speed so that the wealth of
information out there will make more sense. For the sake of clear
writing, I'll write with a voice of authority, although I'm admittedly
not an expert. Please keep that in mind. I'm open to any corrections
that aren't excessively pedantic.

As we get in to computers, I'll also focus on Mac OSX, which
unfortunately isn't supported nearly as well as Windows and even Linux
in the radio world.

### How to Listen

The first step is just to start listening to traditional AM broadcast
signals on shortwave. There are a couple of ways to do this.

#### websdr.org

This one is actually cheating. Rather than setting up a receiver on
site, you'll piggy back on receivers run by volunteers around the
world. They're capturing broad swaths of the radio spectrum and
sending them to your computer for final processing. But it's a good
quick way to get started, it's totally free, and you don't need to
wait two days for Amazon to deliver equipment, so lets start here.

To start, simply go to [websdr.org](http://websdr.org/) and pick one
of the many sites listed that are hosting software. I wanted one that
was near me geographically, so I could compare the performance of an
expertly configured device against my own. You might want to do the
same. For me, that was [k3fef.com](http://k3fef.com:8901/).

Click on that site, and it will drop you in to an interface and
probably play static over the speakers. There are two important things
to see here.

First is the list of radio bands. Various frequencies are
grouped in to bands listing approximate wavelength, for example an 80
meter band. To get started you'll want to click on the **all bands**
option at the top of the screen to list everything available at the
particular site you're on.

The second thing of interest is the waterfall. This shows the amount
of energy being received across the spectrum. The brighter it is, the
bigger the signal that is being received.

So for now, find a particularly bright band, and click on it with the
mouse. Your web browser will tune in to that, and hopefully you'll
hear some AM radio! As mentioned above, there are several different
transmission formats, and some of them are digital, so you might hear
something that sounds like nonsense. For now, just try clicking on a
few bands and listening. Later, we'll get in to some of the different
formats and options.

#### rtl-sdr

If you want something similar to websdr, but feel like that's
cheating, and want to listen directly to the radio waves floating
around you, you can go buy a low priced **Software Defined
Radio**. These are interesting little devices. They were originally
sold as USB Television Tuners when we still broadcasted analogue
transmissions and before we cut over to HDTV. But hackers figured out
that they contained general purpose radio receivers, and took full
advantage of that.

These are nice devices, and surprisingly cheap. You can get one with a
bunch of accessories for under $30. I went with the site
[rtl-sdr.com](https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/)
. This has one that can be hacked to listen to shortwave, not all
can. They will still receive a lot of interesting frequencies, just
not the low ones I talk about here.

Since these are *Software Defined* you'll need software to run
them. On Mac OSX, the only one I found that worked well is
[CubicSDR](https://cubicsdr.com/). This also seems to work on Windows,
although most Windows documentation tends to refer to SDR#,
which I haven't used.

**TODO: Shortwave setting on CubicSDR**

If you have a rtl-sdr, and you have the software, you can fire it
up. You'll get a waterfall similar to the websites above, but it will
be limited to one group of frequencies at a time. The above sites use
multiple devices to serve up multiple bands. The interface is a little
clunky and takes some time to get used to, and now you'll need to know
a little bit about bands to find a good source of tranmissions. You
might want to choose a starting frequency of 6000 or 9500 Hertz if you
want to get some decent radio stations to choose.

#### Portable radio

The last option is to buy a portable radio that has shortwave
(sometimes called **world band**) frequencies. The advantage of this
is that it's small and stand-alone, and doesn't need to be tethered to
a computer. The disadvantage is that you won't get a waterfall display
to easily see what signals are out there. Prices start low and you can
get a pretty decent one for $150 or so. I went with a Tecsun
PL-660. [Here is a list](https://www.amazon.com/shop/thereportoftheweek)
of radios from an enthusiast that I think is a good place to find one
that matches your budget without combing through reviews.

These tune more like your car radio, and vary dependent on model, so I
won't get in to details on tuning.

> LAB - Listen To A Broadcast Radio Station
>
> Now that you have a radio, you can start listening. Broadcast radio
> stations are the easiest. You just find them and listen. A few good
> frequency ranges to scan are from 5900 to 6200 kHz, and 9400-9900
> kHz. You can also refer to a
>[comprehensive list of international broadcast bands](https://en.wikipedia.org/wiki/Shortwave_bands#Internationsal_broadcast_bands)
>to find more stations.
>
>
> Next, look at a programming schedule such as the one at
> [Shortwave Schedule](https://www.shortwaveschedule.com/) and see if
> can tune in to any of the programs listed as **On air now**.
>
> Next, you can find a random shortwave program out there on your own, and use a
> a program guide to see what you are listening to, and where it is
> broadcasting from.
>
> Lastly, go through a band of frequencies and see what the farthest
> station you can receive is. Tip: You will get more distant stations at night.

### Happy listening!

In this part, we've gone over the basics of just receiving some
international shortwave broadcasts and listening. In Part Two, we'll
cover a few various signal formats and use that to listen to amateur transmissions.


