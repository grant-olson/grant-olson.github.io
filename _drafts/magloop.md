---
layout: post
published: true
title: The KC3MLC Magloop
category: news
---

<img src='/assets/img/four_foot_loop.jpg'  />

 I built my own MagLoop and wanted to take a moment to share everything
I learned on the project. The design is moderately portable and
defaults to a 4 foot diameter loop where I've maded plenty of FT-8
contacts on 20-80 meters. In addition, the loop can be swapped out for
a smaller 2 foot loop to work higher frequencies, and a larger 8 foot
loop to get better effenciency on 40-80 meters.


## Basic Theory

One problem I had while learning about this was that things just
didn't make sense! Everything I've read before said loop antennas need
to be 1 wavelength, dipoles 1/2, and verticals 1/4 with ground
radials. How on earth can a loop that's only 1/8th to 1/4th a
wavelength, fed by another element that's 1/5th the size of that (so
1/40th to 1/20th of a wavelength) even work at all?

Lets start with the loop. You have a single loop of wire attached to a
capacitor that is used to finish tuning. We know that a coil of wire
creates an inductor. In this case the loop is a coil with exactly one
turn and does act as an inductor. Combined with the capacitor, we now
have a LC circuit which will resonate at appropriate frequencies.

Next up is the driven element, a loop of wire connected to your
transmitter and placed inside the loop. Once again, even though this
is a single loop, it's also a very simple coil of wire. And what
happens when you place two coils of wire next to each other? You get a
transformer. The driven element puts out the energy, the loop receives
it, and we're good to go.

## Design Goals and Decisions

### The Capactior Was Absurdly Large!

As you read up on designs, one thing that quickly becomes apparent is
that you need a variable capacitor that can handle very high voltages
if you want to operate at higher than QRP levels. I'm still only operating at
100 watts. The answer to this is to get a vacuum-sealed variable
capacitor. One good affordable source is Soviet-Era Russian Surplus
equipment on eBay. While looking around, I decided to go big or go
home! I ordered a 10-500 pF capacitor that was rated at 10,000 volts.

I didn't realize how big I had gone until the thing arrived. It was
huge! *Get Dimensions* and weight in at something like 6 pounds! Many
loop designers say the antenna works better if the capacitor is on the
top of the loop. This simply wasn't going to happen with a capacitor
this big and heavy. I also had dreams of a loop that I could easily
mount on a stick to get some height off of the ground. That wasn't an
option either given the size of this beast!

My design was altered to put the capacitor in a big plastic box that
would act as a base for the antenna. It can go on a table or even a
upside down 20 Gallon bucket, but needs some sort of stand to get
distance from the ground.

### Portable

I wanted my design to be portable in several ways. Many designs out
there involve making an 8 or 16 foot high octagon out of copper pipe
that has been braised together! I wanted something that I could throw
in to my car and hopefully try a POTA excursion one day. And I at
least wanted to be able to get the thing inside my house or garage
without having to take a cutting torch to it!

Based on this, the main loop is just good old RG-213 coax with the
shield acting as the loop. As an added bonus, this allowed me to make different
swappable variations of loop sizes easily.

### Temporary

This is indended to be used onsite temporarily. It's not intended to
be mounted permanantly. As such I tried to make it a little weather
resistant in case there's some light rain, but made no attempt to make
it fully water or wind proof.

### No electronics

Most designs include a motor that spins the tuning capacitor at a
distance. I had serious problems with a motor attached to a smaller capacitor on
a previous build. For the moment I wanted thing to be predictable and
not have to analyze how a bunch of coils of wire in the motor or other
wires going to a control unit. So the capacitor is tuned by hand.

This has worked reasonably well while I've been testing on FT8, and
also shouldn't be a problem if I park on a frequency to do POTA. In
the future I may try to add in a tuning motor, but for now I want to
get a feel for how the antenna performs without having to think about
that complication.

## Build

### Capacitor Mounting and Base

As I mentioned, when my capacitor from Ukraine arrived after six long
weeks, it was bigger than I expected by far! But I was ready to get to
work. So I went to my local big box hardware store and found a 12 inch
by 12 inch plastic electrical junction box. If I had been more
patient, I probably could have found something cheaper.

I drilled holes on two sides to mount some PL-239 adapters. I used an
extra long bolt and two washers to attach some 14 gauge wire to the
adapters, and then attached this to the variable capacitor with hose
clamps. This was enough to start using things, but it eventually
became frustrating to tune the capacitor as it wobbled around. To
reduce the wobbling, I bought a piece of 6 inch wood, and cut it it
fit inside the box. I then used some old nylon straps to secure the
capacitor to the board with a few screws.

> Note on imprvisation with parts

<img src='/assets/img/loop_capacitor.jpg' width='50%' style='float=right;' />

I also drilled six holes to allow me to use three U Bolts to hold the
mast in place. This completed the base.


### PVC Supports

<img src='/assets/img/pvc_supports.jpg' width='35%'
style='float:right;' /> I
made a set of composable PVC support
pipes that would allow me to
easily set up loops of either 2, 4, or 8 foot diameters. My big box
hardware store had pre-cut 2 foot sections of pipe, and I went for
this rather than cut them myself. I also bought adapters to get the
following combinations. The pipes were 3/4 inch and had an outer
diameter of 1 inch.

* One pipe with a four way adapter attached to one end which I'll
  refer to as the **crossbeam**.

* Two pipes with T adapters to support the sides of the RG-213 loop
  which I'll refer to as the **side supports**.

* <img src='/assets/img/pvc_top_support.jpg' width='20%' style='float:right;' /> One pipe with a modified T adapter to support the top of the loop
  which I'll refer to as the **top support**. A
  slot was cut in to this so the main loop, along with the driven
  element could be hung on the adapter without having to pass through
  it. To do this I put the adapter in a vice and cut it with a hacksaw
  *before* attaching it to the pipe section.

* Four pipes with a standard coupling attached to one end which I'll
refer to as the **extensions**.
extenders.

The attachments were glued on with PVC primer and glue. **DO THIS
OUTSIDE!** I made the mistake of doing this on our enclosed porch, and
fumes still managed to pervade the entire first floor of our house. We
had to open the windows and air it all out. It is highly recommended
you use more ventilation.

To assemble the small version with a 2 foot loop, I attached the
single modified T adapter to the base.

For the four foot loop, I added the three T adapters to the four way
adapter.

The eight foot loop is the most complicated. I add the extenders
simply enough, but then needed to use support ropes to keep the
assembly from drooping, and used guy wires on the final
installation. I'll get in to more details later after going through
initial configuration of the four foot loop.

### Loops

### Driven Elements

### Stand

* Note on height testing. 17 inch and up.

### Tuning

Initial tuning is done by adjusting the variable capacitor. I attached
a key ring to mine to make it easier to turn. When the capacitor is
fully retracted and has the *least* amount of capacitance, you'll be
closest to the ideal performance for the given loop size. At this
point, very small changes in capacitance will have a large impact on
the frequency sweet spot. As you add more capacitance and the
frequency goes down, you'll find that the bandwitdth decreases and
that you'll need to move the capacitor more to move the frequency.

When you initially tune the antenna, you'll probably have a poor
best-case SWR. Once you're within range of the band you want to
transmit on, you'll need to adjust the driven element positioning to
get the best possible SWR. This is done through experimentation and in
my experience relies on two factors:

1. How much of the top of the driven element contacts the loop.
2. How far up or down the bottom of the driven element is from the
main loop.

You'll need to experiment for yourself. Here are some notes on what
worked for me. These are not intended to be perscriptive; you'll need
to find your own settings. They will just give you a starting point of
adjustments to try:

* The driven elements should be close to the plane of the main loop,
  but don't worry about the support pipes preventing you from getting
  that last 1/2 inch.

* <img src='/assets/img/two_foot_driven_element.jpg' width='15%'
  style='float:right' /> My driven element for the 2 foot loop most resembles what you'll see
  in diagrams and is a nice round circle attached to the top of the
  antenna. Even still, some movement up and down on the bottom half
  helps as I switch between various bands.

* <img src='/assets/img/four_foot_driven_element.jpg' width='15%'
  style='float:right' /> My driven element for 4 foot worked best with the most surface area
  possible attached to the main loop, and the bottom half located
  extremely high, making a crescent shape.

* The driven element for the 8 foot worked best in a kite shape, with
  barely any contact on the top of the loop. This also benefited from
  adjusting the position of the bottom on different frequencies. In
  general as the frequency goes down, pulling the bottom down helped
  on both this and the two foot antenna.



## Performance

> [pskreporter.info](https://pskreporter.info/pskmap.html) provides
> a great way to see how your antenna is doing when you're working
> digital modes. Various stations send signal reports to a centralized
> location where you can view how well things are propagating, even
> when people are ignoring your CQ calls.
>
> Note that if you want to help out others and send your FT8 reception
> reports, you must enable this in WJST-X settings under **Reporting**.


All results shown are from grid square EN90 running a Yaesu FT-857d at
100 Watts.

#### 2019-Nov-06

Afternoon with the 4 foot antenna sitting on a bucket tuned to 20
meters sitting outside pointing exactly North-South. Scored my first
Alaska contact and a couple hits in Brazil.

<img src='/assets/img/20m_bearing_zero_alaska.png' width='100%' />


### Setting up the 8 footer!

Holes were drilled in to the two base pieces to attach the cable.

* Explain knots and holes!

### Support Tension Lines

* Tip: Cutting Paracord. Don't Saw. Melt briefly with grill lighter.

