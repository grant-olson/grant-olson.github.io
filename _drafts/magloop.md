---
layout: post
published: true
title: KC3MLC's Magloop
category: news
---

<img src='/assets/img/magloop/4ft_loop.jpg'  />

 I built my own MagLoop and wanted to take a moment to share
everything I learned on the project. The design is moderately portable
and defaults to a 4 foot diameter loop where I've made plenty of FT-8
contacts on 17-80 meters. In addition, the loop can be swapped out for
a smaller 2 foot loop to work higher frequencies up to 10 meters, and
a larger 8 foot loop to get better efficiency on 30-80 meters.

Need the TLDR on the antenna's performance? [Click Here!](#performance)

## Basic Magloop Theory

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

### Absurdly Large Capacitor!

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
mount on a mast to get some height off of the ground. That wasn't an
option either given the size of this beast!

My design was altered to put the capacitor in a big plastic box that
would act as a base for the antenna. It can go on a table or stand,
but needs some sort of stand to get distance from the ground.

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

This is intended to be used onsite temporarily. It's not intended to
be mounted permanently. As such I tried to make it a little weather
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

I drilled holes on two sides to mount some PL-239 adapters.

> **ProTip&trade;:** If you're starting to make a bunch of amateur
> radio gear and you don't have a [STEP DRILL BIT](https://www.google.com/search?q=step+drill+bit) you'll want to get
> one as soon as possible. It allows you to drill large holes for
> things like SO-239 adapters without having to switch out bits
> repeatedly and without melting ABS plastic. A set is very affordable
> and probably available at your local Harbor Freight.

<img src='/assets/img/magloop/capacitor.jpg' width='50%' style='float:right;' /> I used an
extra long bolt and two washers to attach some 14 gauge wire to the
adapters, and then attached this to the variable capacitor with hose
clamps. 

I also drilled six holes to allow me to use three U Bolts with wing nuts to hold the
mast in place. This completed the base.

This was enough to start using things, but it eventually
became frustrating to tune the capacitor as it wobbled around. To
reduce the wobbling, I bought a piece of 6 inch wood, and cut it it
fit inside the box. I then used some old nylon straps to secure the
capacitor to the board with a few screws.

> **ProTip&trade;:** Note on improvisation with parts



### PVC Supports

<img src='/assets/img/magloop/pvc_supports.jpg' width='35%'
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

* <img src='/assets/img/magloop/pvc_top_support.jpg' width='20%' style='float:right;' /> One pipe with a modified T adapter to support the top of the loop
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

### Loops

I will generally refer to the loops by diameter which is also the height of the
PVC supports. These are nice round numbers where the actual size of
the loop is diameter times pi. In addition there is some amount
subtracted from the ideal cable length to account for the area of the
loop where the capacitor sits and there isn't any wire.

The loops are made of RG-213 coax with PL239 plugs attached at each
end. This allows me to insert the loop into the support frame and
screw it in to the base.

The actual cable length was:

* **2 Foot Loop** - 5 foot, 9 inches
* **4 Foot Loop** - 12 foot, 8 inches
* **8 Foot Loop** - 23 foot, 6 inches

The 8 foot loop ended up being slightly shorter because it was
drooping more, and I went with more of a diamond shaped loop to avoid this.

If you're trying to build your own antenna, rather than measuring out
to these lengths, I would attach a PL239 adapter to one end of the
coax, feet it through the supports and screw it in, and then mark off
the appropriate place to cut the other end of the cable. This will
give a better loop if your project box or SO-239 placement is
different than mine.

### Driven Elements

The driven elements are made from RG-8X and should be 1/5 the size of
the loop coax. I did not try to factor in the full loop size including
capacitor and just used the physical coax length divided by 5. There
also needs to be a connector to hook the driven element up to your
feed line, so when you cut the cable add 6 to 8 inches or more
depending on how confident you are about getting the coax stripped and
adapter installed on the first try. I used a female BNC adaptor but
feel free to use whatever adapter you want.

There are many confusing designs available for the driven element. In
the end I chose the least confusing one. This involved soldering the
**center conductor** of the coax to the **shield** where the loop will
be the appropriate size. I still don't understand exactly how this
works, but it does. RF behavior on what intuitively seems to be a
short can be a bit strange at times.

** IMAGE OF COAX **

I stripped one end of the coax with a standard stripping tool, and
then gently cut away the outer insulation for about 1 cm at the
appropriate length. I then soldered taking care not to melt the
dialectic material under the shield. After that I added an adapter to
the free end.

## Installation

At this point you should be ready to do an initial smoke test. I would
recommend using the 4 foot antenna and setting the assembly on a table
before trying to use it outside.

### Setting up the loop

I would suggest starting with the 4 foot loop, which can get some
reception on 20, 40, and 80 meters. I would also suggest using a
convenient workspace such as a table in your hamshack since it will
take some time to perform initial configuration of the driven
element. Once the driven element is configured, it will be easier to
set up in a more desirable location.

To assemble:

1. Add the two side supports and top support to the crossbeam.
2. Insert the crossbeam in to the U bolts and tighten the wing nuts to
hold it in place.
3. Hang the loop cable inside the top support, and thread the ends
through the side supports.
4. Screw the PL-239 connectors in to the base unit.
5. **First time only** Insert the driven element in to the top support,
   and experiment with positioning as described in the
   [tuning section](#tuning-the-driven-element) securing with temporary tape. Once you
   find a workable position, permanently tape it in to place in a way
   where it can be easily removed when the main loop is removed from
   the support tubing.
6. Attach the feed line to the driven element.

It should loop like this:

<img src='/assets/img/magloop/4ft_initial_config.jpg' />

After initial testing you may want to set up the two foot loop. The
procedure is basically the same, but you'll use the Top Support only
on the base:

<img src='/assets/img/magloop/2ft_loop.jpg' />

### Positioning

To get lowest SWR, the loop must be off of the ground. While testing
on my back porch, I just sat it on a bar-height table. When I use the
antenna outside, I set it on an upside-down utility bucket which is
about 18 inches high. Any lower than that and the SWR starts creeping
up again. I suspect even higher is still better, as you'll get better
angles for DX takeoff, but the bucket was adequate to hit Europe and
South America from Pittsburgh.

The antenna also has some directionality, with the strongest signal
shooting off of the ends of the loop. It's only somewhat directional,
so I generally just point it either North/South or East/West and don't
try to dial in on an exact bearing.

### Tuning

#### Tuning the Capacitor

Initial tuning is done by adjusting the variable capacitor. I attached
a key ring to mine to make it easier to turn. When the capacitor is
fully retracted and has the *least* amount of capacitance, you'll be
closest to the ideal efficiency for the given loop size. At this
point, very small changes in capacitance will have a large impact on
the frequency sweet spot. As you add more capacitance and the
frequency goes down, you'll find that the bandwidth decreases and
that you'll need to move the capacitor more to move the frequency.

#### Tuning the Driven Element

When you initially tune the antenna, you'll probably have a poor
best-case SWR. Once you're within range of the band you want to
transmit on, you'll need to adjust the driven element positioning to
get the best possible SWR. This is done through experimentation and in
my experience relies on two factors:

1. How much of the top of the driven element contacts the loop.
2. How far up or down the bottom of the driven element is from the
main loop.

You'll need to experiment for yourself. Here are some notes on what
worked for me. These are not intended to be prescriptive; you'll need
to find your own settings. They will just give you a starting point of
adjustments to try:

* The driven elements should be close to the plane of the main loop,
  but don't worry about the support pipes preventing you from getting
  that last 1/2 inch.

* <img src='/assets/img/magloop/2ft_driven_element.jpg' width='15%'
  style='float:right;clear:both;overflow:auto;' /> My driven element for the 2 foot loop most resembles what you'll see
  in diagrams and is a nice round circle attached to the top of the
  antenna. Even still, some movement up and down on the bottom half
  helps as I switch between various bands.

* <img src='/assets/img/magloop/4ft_driven_element.jpg' width='15%'
  style='float:right;clear:both;overflow:auto;' /> My driven element for 4 foot worked best with the most surface area
  possible attached to the main loop, and the bottom half located
  extremely high, making a crescent shape.

* <img src='/assets/img/magloop/8ft_driven_element.jpg' width='15%'
  style='float:right;clear:both;overflow:auto;' /> The driven element for the 8 foot worked best
  in a kite shape, with
  barely any contact on the top of the loop. This also benefited from
  adjusting the position of the bottom on different frequencies. In
  general as the frequency goes down, pulling the bottom down helped
  on both this and the two foot antenna.

  As pictured, you can see
  velcro holding the loop in a position which is best for 40 and 80
  meters, and I move it up for 30 meters.

## Setting up the 8 footer

<img src='/assets/img/magloop/8ft_front.jpg' />

#### Tension lines

The 8 foot loop requires both tension lines to hold the PVC supports
in place. I
drilled X/Y INCH holes in the support element that could pass
through paracord. Holes were drilled through both sides of the pipe.

Hole Placement:

* **Side and Top Supports** A hole close to and parallel with the T
Adapters.

* **Crossbeam Support** Two sets of holes, near the base with enough
  room for the extension element, perpendicular to each other with a
  half inch space between them.

* **Three Extensions** One hole drilled all the way through near the
coupling.

* **The Base Extension** Two sets of holes like the crossbeam support,
  but located so that they are *above* the capacitor housing so it
  doesn't interfere with the tension ropes.

I cut appropriate lengths of rope and melted the ends shut with a
grill lighter. If you smell burning plastic and see smoke, you've
melted them too much.

I assembled the inner set of supports, which consists of the crossbeam
and three extensions with normally placed holes. I tied a knot in one
end of the paracord, inserted it through one set of holes at the base
of the crossbeam, then worked my way through the other three supports
running the line through the hole, pulling it tight, and tying a clove
hitch. Once these three supports were secure, I ran the line through
the second set of holes on the crossbeam, tied a tautline hitch, and
pulled it tight.

I then added the remaining supports and did the same thing. It is best
to (1) do this outdoors, and (2) only insert the supports as you're
ready to tie them in place.

At this point you can mount the support in to the base and tighten
down the U Bolts. Although you'll need guy lines for long term
installation, this will sit fine on flat land without falling over.

> **ProTip&trade;:** Install the loop and attach the driven element to the feed
> line before inserting the support in to the base.

#### Guy lines

The 8 foot loop also needs guy lines. I cut paracord to three appropriate
lengths, melted the ends closed, and tied a bowline knot on one
end. After that I looped them around the center mast, placed the
antenna on my support bucket, and secured the lines to stakes with
tautline hitches and applied tension.

A helper is extremely useful here especially when the land is not
flat, but you can usually find one line to secure first while holding
it tight and then go back and get the other two lines in to place.

You will probably also want a step stool to adjust the driven element
and find optimal positioning.

## Performance

> **ProTip&trade;:** [pskreporter.info](https://pskreporter.info/pskmap.html) provides
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

<img src='/assets/img/magloop/20m_bearing_zero_alaska.png' width='100%' />


#### 2019-Nov-09

A half-hour in the afternoon with the 8 foot antenna facing East/West on 30
meters. Contacted Italy a few times, as well as Croatia, Crete, and
the Balearic Islands. PSKReporter showed one bounce to Australia but
alas it didn't hold out!

<img src='/assets/img/magloop/30m_8ft_ew.png' width='100%' />

#### 2019-Nov-10

A half-hour in the morning with the 8 foot antenna facing East/West on
40 meters. We are getting further than ground-wave, but only high angles
are making it back, giving an effective radius of somewhere between
750-1000 miles with a few outliers.

<img src='/assets/img/magloop/40m_8ft_morning.png' />


