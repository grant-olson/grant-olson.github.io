---
layout: post
published: true
title: Can Crusher Part 2 - Stepper Drivers and Controls
category: news
---

Now that the frame is completed the next big task is to write out the
drivers to control the stepper motors. At this point everything will
be on a breadboard but I'll eventually make up some PCBAs for the
final product.

# Basic Design

* Two independent stepper motors are on each side of the unit.
* On boot the device should home by going down until the base of
    the unit is identified by both stepper motors.
* From there move up X mm to make space for a can.
* When can is detected, lower and crush the can.
* There will probably be additional logic when we first touch the
    can and feel resistance.

# Motor Controller boards

I decided to use some TMC2209 driver boards manufactured by
BigTreeTech. These are mostly built for home made 3D printers and CNC
machines and have a configuration that allows you to plug them in to
the existing boards out there designed to control those machines. I'm
going to use these boards but create my main control board.

The TMC2209 was chosen because it has built in stall-detection, and
I'm hoping to get auto-homing, like you see on higher end 3D printers
like Prusas. I'll detect the end of the range of motion when stall
detection kicks in.

The cheap solution is to install limit switches that are just some
ribbon wire that gets physically pressed to complete a circuit. These
are okay, but I find them annoying because they usually act as a proxy
for the actual limit, aren't so accurate, and can move a little bit
over time.

I'm also anticipating a stall when the plate initially hits the can so
if that's the case I'll also be able to detect when we are held up on
a can and not actually hitting the base of the unit.

# Initial Verification

I wanted to test out the boards before hooking up to my Pi Pico. The
simplest possible working configuration should be:

* A stepper motor is hooked up.
* Power is applied to both the control portion and the motor portion,
    probably at different voltages.
* A variety of pins need to be set to either GND or VCC to enable
    desired behavior.
* A steady clock signal should be applied to the STEP pin and we should
  see the motor move.

This is a good example where accumulating gear over the years helps
out. In my earlier days I would have skipped this step and gone
straight to hooking up to my Pico (or Arduino back then) and would
bang my head against the wall until things work. I also would have
done something silly to deal with the fact that the motors have
different power requirements than the logic portion of the board, like
connecting a nine-volt battery. Then I would need to write test code,
and also couldn't quite be sure whether the code had problems or the
hardware setup did.

But with a bench power supply or two, and a signal generator I was
able to create an initial test that didn't require any sort of
microprocessor. I sent 5 volts to the VCC, 9 volts to the VM (Voltage
Motor) pin, and did some quick math to determine that I wanted my
signal generator to send out a square wave at 1600 Hz to rotate the
motor at one Revolution Per Second. (Test motor spec'ed at 1.8 degrees
per step, 200 steps for a full circle, and the TMC2209 has 8
sub-steps, so 200 * 8 = 1600)

Annoyingly the boards have a pin configuration that won't let you plug
them directly in to a breadboard without shorting 3 pins. luckily the
pins are also exposed on the top side so I was able to cut off two
pins on the bottom side:

<a href='/assets/img/can-crusher/annoying-pinout.jpeg'><img src='/assets/img/can-crusher/annoying-pinout.jpeg' width='50%' /></a>


Even with the simple test setup, I managed to fry a board (or two if
I'm honest!) before getting the configuration correctly. One thing
that complicates these stepper boards is that they motor side of the
chip can want to draw 1 or 2 amps or power, at a higher voltage than
the logic side supports, so a problem with the wiring can send way too
much voltage and current to the logic side, frying it. Normally I
would avoid this by keeping the current limits on my bench power
supplies low, but in this case I needed that current to spin the motors.

But in the end my test setup worked and the rotational speed looked
correct to the eye.

<a href='/assets/img/can-crusher/initial-tmc-verification.jpeg'><img src='/assets/img/can-crusher/initial-tmc-verification.jpeg' width='100%' /></a>
