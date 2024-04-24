---
layout: post
published: true
title: Can Crusher Part 5 - Mechanical Updates
category: cc
---

Now that my software is in good shape and I can easily test out the
can crusher, I'm starting to see a lot of problems with the initial
design. This is expected. I just dove in and built the first prototype
without much analysis. It's time to take another pass on the
mechanical design and get things whipped in to shape.

Let's take a look one problem at a time:

# The Can Will Pop Out of the Device

The first problem was relatively simple. When the crushing element
applied pressure to the can it could come flying out of the front of
the device!

The fix: I made a tray that holds the can.

<a href='/assets/img/can-crusher/can-holder.jpg'><img src='/assets/img/can-crusher/can-holder.jpg' width='50%'  /></a>

# Acrylic Structural Elements Still Have Too Much Flex

The next problem was that the acrylic still flexed a lot when pressure
was applied to the can to crush it.  It was much, much more rigid than
a 3D print would be, but it was flexing enough to cause problems with
the lead screws.

The fix: I got the structural elements made out of 1/4 inch thick
laser cut aircraft aluminum.

I'd never done this before but decided to give OSHCut a try. I've
always felt guilty for not using their sister service OSH Park to get
my PCBs made, but the bids always ended up being an order of magnitude
higher than the Chinese PCB vendors. OSHCut worked great! They have a
nice site where you can drop in a .step file and get an instant
quote. This is great if you're like me and don't know what the hell
you're doing. Other companies wanted me to talk to an engineer without
even a ballpark quote!

The price here was a little at the high end for a hobbiest project and
will probably be out of some people's budgets. For the frame top and
bottom, and crusher element I probably needed a 9 inch by 9 inch piece
of stock. They have a back-of-the-line price that means it takes a few
weeks to get your parts, vs 2 or 3 times as much to start production
the next day.  Great for someone on a hobby budget.  Still, at $126
for a single run, this will probably be the single most expensive part
of my project. But at this point I'm committed to getting results.

That price would be frustrating if I ordered something and I got the
dimensions wrong and it was unusable and needed to order again. If I
want to do aluminum parts in the future I think I'll probably still do
a test run on the CNC with cast Acrylic first, and make sure it's
perfect before risking a bad run on the laser cut aluminum.

Side-by-side: 3D printed version, acrylic version, and final aluminum version. And the installed platform.


<a href='/assets/img/can-crusher/mounted-aluminum-crusher-element.jpg'><img src='/assets/img/can-crusher/mounted-aluminum-crusher-element.jpg' width='50%' style="float:right;" /></a>
<a href='/assets/img/can-crusher/print-cnc-laser-cut.jpg'><img src='/assets/img/can-crusher/print-cnc-laser-cut.jpg' width='50%' /></a>

# The Steppers Don't Create Nearly Enough Crushing Force

After getting a solid platform that was didn't flex, the next problem
was that I didn't have nearly enough crushing force. I expected things
to be difficult, and anticipated having to add something the crushes
the can sidewalls before applying pressure from the top. But even a
can partially crushed by hand wasn't getting anywhere.

One option to fix this would be to just buy bigger an bigger stepper
motors until we had power. I'm currently using NEMA 17 sized
motors. But that seemed a bit excessive.

After some research I learned about the different threading available
on lead screws.

> **The More You Know...&trade;**
>
> I was always confused that these lead screws were
> referred to as **trapezoidal** when they are clearly **round**.
> It turns out this refers to the shape of the thread. Rather than
> coming to a sharp point like a normal screw these are flattened
> for a good amount of the thread. 1 mm on my 2 mm threads. This
> creates a more robust mating with the nut that moves the platform
> up and down.

<a href='/assets/img/can-crusher/lead-size-comparison.jpg'><img src='/assets/img/can-crusher/lead-size-comparison.jpg' width='50%' style='float:right;' /></a>

My normal 3D printer lead screws had a 2mm pitch, but they had 4
'starts'. This means there are 4 sets of threads instead of 1 like a
normal screw. Each of the thread are intertwined like the stripes on a
candy cane. (Not sure if that analogy is useful?) So a single rotation
of the stepper motor causes the screws to move the platform up or down
8 mm instead of 2. They do however sell lead screws with 1,2, or
sometimes even 3 'starts'. I was able to buy some screws with a single
start, meaning a rotation now moves the platform 2 mm.


This should give 4 times the force from the same motors, with the
downside being that the crusher element moves at 1/4 the
speed. Installing these finally got me to my first crush of a can that
was even close to acceptable! If I want to get even more power there
are screws with a 1mm lead.

Note the image on the right. These rods have the same thread size, but
the one on the left has a steeper angle. This means it has more starts
and goes further per revolution.


# The Crushing Platform Has Many Alignment Problems

<a href='/assets/img/can-crusher/rails.jpg'><img src='/assets/img/can-crusher/rails.jpg' width='50%' style='float:right;' /></a>

The next problem was that the crushing power would often cause the
screws to come out of alignment, and caused the crusher element to
stop being level. With the 2 mm screws this would become so severe the
system would bind. On the 8 mm screws I could power down the stepper
motors and spin the screws manually to re-level the element. But with
the 2 mm screws I had to actually remove the steppers from there
holders to release enough stress to allow me to align things several
times. That's obviously not going to work.

One design element on my 3D printers started making a lot more sense.
The higher end ones have straight rods of hardened steel that guide
and align the parts. Bearings reduce friction. The screws then have a
lot more leeway in terms of their positioning. They are moving the
platform up and down, but they are no longer responsible for part
alignment.

I added a straight rod on each side on the back of the structure. Then
I 3D printed out some holders for some vertical bearings. This meant
that I needed a new crusher element that didn't interfere with the
rods and had mounting holes for the bearing holders.

Which unfortunately meant another order from OSHCut a week after the
first batch came in! After some test 3D prints and acrylic, I was back
to OSHCut for a redux in aircraft aluminum.


# The Lead Screws Have Alignment Problems

The last big problem was that it was difficult to get proper alignment
of the lead screws. I imagine this was always a problem, but it was
particularly obvious once the straight rods were helping keep the
platform straight.

As I moved down the platform it would get harder to spin the lead
screws. Just a little bit on most of the way down, but it got
especially hard at the very bottom. This was a big problem because my
stepper drivers use current values to determine if they have stalled
out. The difference was enough that the value changed depending on
whether I was at the bottom of the platform, in the middle or at the
top, making it impossible to do stall detection or auto-home reliably.

I was getting in to an issue where we were dealing with tight
tolerances. It seemed like I was < 0.5 mm from getting the lead screws
straight. I could have kept moving things around just a little,
printing, and testing the range of motion. That would be
time-consuming and wouldn't really fix the problem. There are lots of
things that introduce tolerance errors:

* My 3D prints only have 0.4 mm resolution. Parts my be microscopically off
    from a different 3D printer.
* My professionally cut laser parts seem to have more accurate and slightly
    different sizing.
* I'm not using precision assembly or measurement. Parts are held together
  with T-Slot screws. Having the left and right side servo holders vertical
  alignment off by 0.1 mm or less, might change the proper location of
  the motors.
* I could get everything perfect, then drop the thing on a concrete floor,
  lose a small amount of alignment. and be back to printing new variations.

I needed a solution where I can manually square the device, rather than
assuming all my parts are printed at perfect sizes, assembled in
the same way, and aligned, to account for all these various factors.

<a href='/assets/img/can-crusher/stepper-holder-with-slots.png'><img src='/assets/img/can-crusher/stepper-holder-with-slots.png' width='50%' style='float:right;'  /></a>

I changed the mounting holes for the stepper motor in slots and came
up with a procedure to square the machine. I would do an initial
install of the steppers, keep the screws loose, then manually lower
the crusher platform as far as it would go. This would move the
steppers in to proper alignment and I could tighten them. Then I could
do a few manual tests and make sure that I felt the same tension
across the range of motion, and slowly lock the screw down.

That introduced the problem that the rod holders I placed on the top
of the unit had a strict value that couldn't be adjusted. And slots
don't make sense because they may need some play on both the X and Y
axis'. Looking at my 3D printer for inspiration I noticed that it
didn't really even hold lead screws the lead screws in place at the
top. The lead screw holders at the top of the lead screws have several
millimeters of clearance and are there to just be there to catch the
screws if something goes really, really, really wrong and the machine
is falling apart. I decided to borrow that approach.

With all these fixes I was able to go back to running my test script
to find appropriate stall current settings. Things were much more
reliable. I produced the same values no matter where I was vertically
on the device. And the results were reproducable. The downside is that
I may need to occasionally re-square the device if it gets a lot of
use.

# And Back to the Rod Lead Size

Once my new batch of aluminum for the crusher plates arrived I
installed it. I haven't mentioned this before, but I currently have
two can crushers. The first one lets me do a lot of rough testing and
the second has a more stable configuration. One is the alpha and the
other is the beta version of the product.

I upgraded my alpha crusher first. This one still had the
fast-yet-less-powerful 8 mm lead screws. It turns out that with all
the rigidity in place, and all the changes I made, these were actually
much, much better at crushing cans than they were in earlier
tests. The slower-more-powerful lead screws on the beta crusher had
been extremely slow, maxing out at 8-9 mm per second, and very
difficult to dial in the stall settings.

Based on that I decided to go back to the original 8 mm lead screws
and use those as I move forward on the project.

# Next Up

I now have a mostly working system but I'm running it via a USB cable
on my computer manually. I originally expected to have a high level
control computer talking to my low level Pico PCB. This would allow
the high-level computer to handle the user interface and more
complicated calculations. I've decided it's time to take a first pass
at that. My goal get to the point where I have a self-contained unit
that can run at the press of a button, and not a CLI script. Then I'll
have my first full version of the entire stand-alone can crusher.

**********************

*All cad files, electronic files, source code, etc, referenced in this
 post series is available on [my github
 page](https://github.com/grant-olson/can-crusher).*