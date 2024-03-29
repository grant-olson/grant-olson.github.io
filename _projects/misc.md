---
layout: post
title: Misc. Projects
category: projects
description: Smaller projects not worthy of unique entries
permalink: /:categories/:title.html
display-order: 5
---

Projects listed from newest to oldest.

## [MyBitSafe](https://github.com/grant-olson/mybitsafe)

A failed attempt at a bitcoin startup.  MyBitSafe provided escrow
services of bitcoin transactions for the price of a postage stamp.
This was apparently too expensive.


## [Droid Quizmaster](https://github.com/grant-olson/droid-quizmaster-scala)

A little Droid application written to become familiar with Droid and
the programming language Scala.

## [pyASM](https://github.com/grant-olson/pyasm)

A dynamic inline assembler that allows you to run x86 code in a Python
application.  This somehow managed to get me a limited amount of
[academic cred](http://citeseerx.ist.psu.edu/showciting?cid=271561).

## Compyler


Compyler is a native x86 python compiler I worked on in 2006 before my
interest moved on to other things. Instead of targeting C, compyler
targets native x86. It even includes its own assembler which generates
native windows COFF files (but no linker so you still need a toolchain
to generate a complete executable).  Motivation

No I wasn't trying to get a faster program. I was primarily interested
in generating python apps with small footprints. Secondarily, I
considering marketing and selling the program as a obfusticater since
that request seemed to come up once every two weeks on
comp.lang.python. Luckily, that didn't work out so I decided to
release the program under a BSD-style license.

The program is basically abandonware. I don't really have any interest
in doing anything more with this in my spare time, and I doubt anyone
would pay me to work on it. That being said, I made a reasonable
amount of progress. Several test cases of python code will
successfully compile and execute. The most complex piece of code to
run is pystones.py (from the standard python distro) but it runs
excessively slow. It looks like I don't have reference counting evened
out. There is a testcase that can generate a primitive .pyd file that
can be imported by python that works, but generated .exes currently
throw an exception when run.

Right now it is windows only. pyasm (the component that generates the
assembly) will run on linux, but it doesn't generate either elf files
or something that a GNU assembler can understand.

* [compyler](/files/compyler.rar)
* [digital signature for compyler](/files/compyler.rar.asc)

## [PyXR](http://sourceforge.net/projects/pyxr/)

PyXR generates cross-referenced, pretty-printed html display of python
source files for reference purposes. It has similar functionality to
LXR.  And yes, it's hosted on **sourceforge**.

## edNinjaHedz

![edNinjaHedz](/files/edNinjaHedz.png)

A silly little 3d 'game' I wrote in 2000 when I was teaching myself
OpenGL and matrix manipulations.  Written in primitive pre-STL
pre-Boost C++.  Code that, in retrospect, is pretty awful.  Don't
judge me on this one...  Compiled on Microsoft Visual C++. 6.0 In
2000, my computer could barely provide a decent frame-rate, so I
didn't incorporate a rate governor.  Thanks to the power of Moore's
law, the frame rate is through the roof in 2010!  It contains a
built-in level editor.  Levels are stored as quad-trees.  Character
graphics were imported from existing id Software .md2 file format, the
format for Quake 2 character.  I didn't design the characters, and
unfortunately the site I downloaded them from didn't provide
attribution, so neither can I.  Apologies to the artists.  And even
greater apologies for the voice work.  That's all me...

To 'play', download the executable zip.  Unzip.  Run either the batch
file or executable.  the arrows will move Ace Frehley.  The control
key will shoot throwing stars.  Try to kill as many Magnetos and
Transformers as you can before dying.  As in life, there's no score,
it's an exercise in futility...

* [Executable Game](/files/edNinjaHedz_exe.zip)
* [Digial Sig for Executable Game](/files/edNinjaHedz_exe.zip.asc)
* [Source](/files/edNinjaHedz_src.zip)
* [Source](/files/edNinjaHedz_src.zip.asc)


## Aqualung

![aqualung](/files/aqualung.png)

Aqualung is a fairly advanced Quake 3 Arena mod written in 2000.  It
turned any level into an underwater level, and allowed players to swim
through them with full six degrees of freedom.

* [Compiled Mod](/files/aqualung1_01.zip)

* [Digital Signature for Compiled Mod](/files/aqualung1_01.zip.asc)

* [Source](/files/aqualung_src.zip)

* [Digital Signature for Source](/files/aqualung_src.zip.asc)


