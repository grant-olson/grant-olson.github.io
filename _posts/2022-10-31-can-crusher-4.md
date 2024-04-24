---
layout: post
published: true
title: Can Crusher Part 4 - Towards Production Software
category: cc
---

At this point I have a working dev board that makes it much easier to
write code than it was on the breadboard. I'm able to deploy code
faster and the can crusher is less precarious, but development is
still slow going.  I'm going to start working on higher quality
firmware, focusing on a control interface that allows me to send
commands over UART to the board. This will make it much easier to try
things out as I develop the system.

There's always a trade off between abstractions and system
control. Python is a great language for development because I can
write code and test very quickly. But it's not such a good language
for embedded systems because I lose a lot of predictability and speed
that's required for motor control, sensors, etc. For that you need a
lower level language which slows development time.

The plan is to keep a smaller core of low-level code that's easy to
follow, and then add an interface so that I can user higher level code
while testing various things out. When all is said and done, then I
either have a pretty good reference implementation of the higher level
activities to port to C, or I can just keep the higher level code
running on another control board that isn't as sensitive to real-time
requirements.

While I'm working on the software I also want to see if I can take
advantage of the PIO feature on Pico, but I'll get in to those details later in the post. For now, the...

# Command Interface

## Basic Design

* Send commands over UART.

* Should be extremely simple language so I don't waste time writing an
  elaborate parser in C.

* Although I will be able to control directly from a terminal, it's
  anticipated I'll end up using some sort of middle-ware in python
  to build and send commands. The language doesn't need to be pretty.

* The interface should be able to report success or failure.

* The interface be able to return results as data when needed.


> **Getting real.** I'm doing this project for fun and to stretch my
> skills. I'm pretending to be each and every member of a team
> building out a full product.
>
> If this was a real product, there's a
> perfectly good motor control language called *Gcode* that probably
> covers all the features listed above and more. Even better there's a
> great open source implementation called [Marlin](https://marlinfw.org/).
> It's targeted at 3D
> printers but has been customized to run a lot of machines. And on
> top of that there are many control boards,
> [some as cheap as $10](https://www.aliexpress.com/wholesale?SearchText=marlin+controller),
> that can run Marlin.
>
> Realistically, I should just be using both that hardware and software
> to control my device.
> It would dramatically simplify development time. But since this is a
> learning experience we'll pretend none of that exists.

## Control Interface

The goal here is to keep things as simple as possible. We won't have
anything like variables, conditionals, loops, turing-completeness, etc. We'll just send a
message and get something back. We will also make it very easy to parse.

At the core the format will be either `COMMAND` to do something or
`COMMAND ARG1 ARG2` if we want to send data. There may be a return
value in the form of `COMMAND: VALUE` There will always be an
indication of success or failure with either `OK` or `ERR 132`

An example communication session would be something like:

```
> WAKE
OK
> HOME
OK
> POSITION?
POSITION: 150
OK
> MOVE 50 10
ERR 109
> POSITION?
POSITION: 173.9
OK
> MOVE -125 20
OK
> SLEEP
OK
```

In this session I:

* Wake up the device so the motors are powered. Stepper can run hot
    and shouldn't be left on 24/7.

* Auto-home the device so we know where the crushing platform is.

* Check the current position, in millimeters, from the base after homing.

* Move the platform up 50 mm at 10 mm per second, **but** we encounter
  an error indicating that the motors have stalled, **meaning** we
  tried to go higher than is possible and hit the ceiling.
  
* Check to see how fare we really moved. 150 - 173.9 means I moved
  23.9 mm before failing.

* Move the platform down 20 millimeters at 10 mm per second, so I'm
  not touching the ceiling.

* Go back to sleep to save power.

This is extremely simple to parse but I can still do everything I need
to build up a complex system.

## Property bag

The interface dramatically improves things. While testing it quickly
becomes apparent that I still need to recompile to change default
values. Now it's time to throw all of those values in to a property
bag.

I [create an interface](https://github.com/grant-olson/can-crusher/blob/main/firmware/pico/property.c) that allows you to get and set all the
properties in a centralized location. It allows you to do this with
either a C `enum` or the name of the property. Then I add a quick
interface to the language `PROP= PROPERTY_NAME PROPERTY_VALUE` to set
and `PROP? PROPERTY_NAME` to get.

I add this and move all the appropriate properties to this system and
things are looking good. Saving this to the Pico's flash so it
survives reboots should be simple. I just need to:

* Choose a region of flash.
* Add some magic value to the beginning so I know if it's been initialized yet.
* Add a version number so the code can be smart when we add more values.
* Save the raw values.

The Pico as usual has a great API and documentation on doing this but
I ran in to a few problems.

### Memory Offsets

First I didn't realize that to read data you are supposed to read from
an absolute address, and to write data you are supposed to write to a
relative address on the external flash storage:

```c

#define PROP_OFFSET (1024 * 1024 * 2) - FLASH_SECTOR_SIZE
#define PROP_ADDRESS ( XIP_BASE + PROP_OFFSET)

// Read access - USES ABSOLUTE ADDRESS
const uint8_t* flash_bytes = (const uint8_t *) PROP_ADDRESS;

// Write access - USES RELATIVE OFFSET
uint8_t data[FLASH_PAGE_SIZE] = {0xFF};
flash_range_program(PROP_OFFSET, data, FLASH_PAGE_SIZE);
```

Note in the last line you send the address of `PROP_OFFSET`, not
`PROP_ADDRESS` to write to memory. I spent way too much time figuring
this out. It makes sense though. A CPU will map various chunks of
memory, be them ROM, RAM, etc, to certain offsets. But the chips that
actually hold values are only internally aware of their local
addresses. Internally everything will map starting at address 0, and
externally the CPU will decide to route (for example) all addresses
from `0x02000000` - `0x02010000` to that memory bank. Trying to write
to the absolute address caused my program to crash hard, essentially
segfaulting, which made difficult.

### Flash programming is **blow-fuse** style

In general the Pi Pico has great documentation and example code. The
best around. I would have saved myself a lot of time on the previous
problem if I would have run the example code earlier. But here I ran
in to a problem that was a little too subtle to show up in the docs I
read.

If you've ever worked on a device with OTP (One-Time Programmable)
Memory, you know that the name isn't entirely accurate. All the bytes
are filled with 0xFF and to change any bit to zero you **blow the
fuse**. Once that's done you can never set the fuse back to the 1
state; you're stuck at 0 for eternity. You can take advantage of this
feature to do some neat tricks. For example you can reserve a bank of
memory for later use as long as you don't write any values
initially. It will just stay full of bytes of `0xff`. Then you can
have some marker in the main program that checks to see if that memory
has been burned later. If it's `0xFF` run the normal program. If you
write an upgrade, and blow the first bit so that it reads `0xFE`, then
run the program starting at memory address `0x2000`, Blow the next bit
so that it reads `0xFC` then run the program at `0x4000`, etc. Neat
trick. The only problem is you can't roll back to running the code at
`0x2000` if there's a problem with the new code.

Although not OTP, the flash memory on the Pico works the same
way. This means you **must** run an erase operation before
reprogramming a chunk of memory. If you don't you end up munging
numbers together. Most obviously, if you previously wrote `0x0` you
will never be able to write anything to that memory address. More
confusing if you don't understand fuse blowing: If you have written
`0xAA` (binary `10101010`) and then try to write `0xF0`, you'll end up
with contents of `0xA0` as the various fuses are blown.

Moral of the story, always erase memory before re-programming on a Pi Pico.

## Python testing framework

Now my control interface is getting sophisticated and I can do a
lot. But I'm still annoyingly typing commands in to a terminal session
with basic capabilities, it doesn't like special keys, I can't
up-arrow to run the last command, etc. This still isn't quite where I
want to be to develop quickly. I need a higher level interface.

I'm able to [whip one up in python](https://github.com/grant-olson/can-crusher/blob/main/firmware/scripts/serial_cli.py). I can take advantage of some of
python's advanced hooks so I don't need to update my code every time I add
a command, and get to the point where I can run the can crusher
through some sophisticated programs.

Now I can easily test how fast I can safely go up and down:

```python

from serial_cli import *

cli = SerialCLI("/dev/ttyUSB0")

cli.home()
start_position = cli.position()

# See where our speed maxes out. incrementing speed
# by 10 mm per second each run.
for i in range(10,100,10):
  try:
    # Jog up and down
    cli.move(50, i)
    cli.move(-50, i)
  except SerialException as ex:
    print("Failed at speed %d with error %s" % (i, ex))
    current_position = cli.position()
    print("Moving to start position at safe speed.")
    mm_to_start = start_position - current_position
    cli.move(mm_to_start, 10)
    
```

## Real World Example - Better tuning of crash detection

Let's return to a problem that was difficult when I was exercising the
stepper motor drivers a few weeks ago. I chose these stepper drivers
because they have stall-detection. This allows me to detect when the
crusher platform hits either the top or bottom of the structure, as
well as when it hits a can. This is done by setting a value for the
current drawn per step. If our current draw drops below that value
then the system decides the motor can't advance.

Unfortunately the actual value is a bit of a magical number. It's a
current draw value, but because these drivers can be used with a
variety of motors with different specifications that are used for a
variety of purposes, there is no one-size-fits-all way to determine
what the value should be. The datasheet isn't able to include any
formulas, for example *to have torque threshold of X, use formula
Y*. The correct values must be obtained experimentally and tuned for
your particular application.

Early on when I was writing unabstracted C without a control language
this was extremely time consuming and frustrating. I would need to:

* Set a test value.
* Recompile.
* Reset the Pico and deploy code.
* Have some sort of test action that hits the limit of motion.
* Hope the motors don't keep running forever when they encounter resistance.

I did manage to find a value for movement that worked but surely
wasn't ideal. I also ran in to problems because the value seems to
change as we change the speed, so really I will need to come up with
some sort of function to calculate something along the lines of *at
speed X mm per second, use value Y*. Additionally, if this was a real
product, we would probably want some sort of field calibration in case
the unit gets knocked around or performance changes with age.

I was able to use python to write a much better system to tune the
numbers than throwing virtual darts in code. It works by:

* Setting the motors to a given speed.
* Maxing out the threshold so the motor will stall.
* Trying to move the platform up and down 10 mm.
* Lowering the threshold value until the platform can complete the range of motion.

This is much, much better, but its still slow. It can run up to 256
times while narrowing in on the value. So I added code to find
approximate ranges and narrow in on them. First in groups of 64, then
16, then 1, to speed things up. For example:

* Test 255, stall. Test 191, stall. Test 127, PASS.
* Test 192, stall. Test 176, stall. Test 160, PASS.
* Test 176, stall. Test 175, stall. Test 174, stall. Test 173, stall. Test 172 PASS.

I can run the test 5 times, come up with an average, and add in a
little bit of a leeway (say 5%) and use that as the value. On top of
it I can run the code over a series of speeds quickly, say from 5 mm
per second to 30, in intervals of 5 mm per second.

Now I *could* write all of this in C, but it would be time
consuming. In fact I might really want it in C later for field
calibration. But at this point I'm still not sure that this is the
ideal algorithm and what other problems I'll encounter. It's really
nice to test quickly, lock down the procedure, then either leave as is
or re-write in C.

I'm able to write a quick python script to do this in less than 15
minutes and less than 100 lines of code:

```python
#!/usr/bin/env python

from serial_cli import *
import statistics
from time import sleep
import sys

cli = SerialCLI("/dev/ttyUSB0")

def retry_wake():
  try:
    cli.wake()
  except SerialException:
    cli.wake()

def narrow_sg_range(bad, good, step, speed):
  sys.stdout.write("Trying ")
  for i in range(bad, good-1, step):
    cli.set_prop("STALLGUARD_THRESHOLD", i)
    cli.sleep()
    sleep(1)
    sys.stdout.write("%i. " % i)
    sys.stdout.flush()
    retry_wake()
    try:
      cli.move(-10, speed)
      cli.move(10, speed)
      print()
      return (i - step, i)
    except SerialException as ex:
      pass # Later check for stall
  print()
  print("Failed to find inflection point!")
  return (0,0)


def find_range_once(speed):
  bad, good = 255 , 0
  bad, good = narrow_sg_range(bad, good, -64, speed)
  if bad == 0: return 0
  bad, good = narrow_sg_range(bad, good, -16, speed)
  if bad == 0: return 0
#  bad, good = narrow_sg_range(bad, good,-8, speed)
#  if bad == 0: return 0
  last_good = good
  bad, good = narrow_sg_range(bad, good, -1, speed)

  # If we didn't get it on 1 we might be on the very edge of stall
  # detection. Try again along that range.
  
  if bad == 0:
    bad, good = narrow_sg_range(last_good+3, last_good-3, -1, speed)
  print("SPEED: %d BAD %d, GOOD %d" % (speed, bad, good))
  return good
  

def find_range(speed):
  results = [find_range_once(speed) for x in range(0,5)]
  print("RAW RESULTS: %s" % repr(results))
  results = [x for x in results if x != 0]
  if len(results) < 3:
    raise RuntimeError("BAD DATA POINTS!")
  average = statistics.mean(results)
  print("AVERAGE: %f" % average)
  safe_average = average * 0.95 # give an extra 5%
  safe_average = int(safe_average)
  print("FINAL: %d" % safe_average)

values = []

# 5 - 84
# 7 - 109
# 10 - 132
# 12 - 144
# 15 - 157
# 17 - 158
# 20 - 171
# 22 - 151
# 25 - 135 ?
# 30 - ???

for i in range(28,36,5):
  cli.set_prop("STALLGUARD_THRESHOLD", 171)
  cli.sleep()
  cli.wake()
  cli.move(10,20)
  cli.move(-10,20)

  res = find_range(i)
  values.append( (i,res) )

  ten_speed = 131 #values[0][1]
  cli.set_prop("STALLGUARD_THRESHOLD", ten_speed)
  cli.sleep()
  cli.wake()
  cli.home()
  sleep(1.0)
  cli.move(50, 10)
  
print(repr(values))
```

## Uncovered problems

Now that my testing is much more systematic and less ad-hoc I identify
a few problems:

1. The stall value seems to change as I get lower and lower on the
   platform.  This makes me think that the threaded rods aren't
   properly aligned and are at slight angles that I can't see. I'll
   need to investigate and redesign the holders.

2. We can't travel nearly as fast as I expect. I suspect it's because
    at higher speeds we need to accelerate, and my current algorithm is either
    on-at-full-speed or off-at-zero. The TMC2209 datasheet does indeed
    indicate that to move swiftly you need some acceleration algorithm,
    and this is up to you to write.

# PIO Stepper Control

Since I'm in software mode and still have a few days set aside in my
make-believe sprint, I move on to another feature I wanted to get
working on the Pico. One of the major reasons I wanted to use a Pi
Pico was to get an opportunity to play with the Programmable I/O
(PIO).

## General High Level PIO Justifications

The RP2040 has two dedicated sub-processors that are optimized for
dealing with input and output. They have a very small footprint,
memory, and set of assembly instructions, and are very
specialized. But the advantage is that they run completely
independently of the main CPU, and each instruction takes exactly 1
clock cycle, so the execution time is extremely fast and predictable.

That's the high level explanation that is given by the Pi
Foundation. After working through the datasheet explanation and SDK
examples, it becomes apparent that these processors are extremely
optimized to turn bytes in traditional memory in to signals on GPIO
lines, and vice-versa. I think that's the best way to think about how
to take advantage of them. How do I turn bytes in to signals, and 1 or
2 signal lines in to bytes?

One SDK example is [UART control](https://github.com/raspberrypi/pico-examples/tree/master/pio/uart_tx). I think this is a really good one. If
you've played around with GPIO pins you've likely played around with
few standard interfaces like SPI or I2C that are easy to
'bit-bash'. They don't have really tight timing requirements and since
you control the clock you can just flip things up and down to make
things happen:

```
def send_bit(bit):
    bit_pin.set(bit)
    clock_pin.set(1) # force it high
    clock_pin.set(0) # force it low.
```

But UART is actually extremely timing sensitive. The first clock
signal of a byte tells you what the clock frequency is, and you need
to be there to pick up the data on that exact timing. Similarly you
need to send data with very exact timing, which is difficult to do
even in a low level language like C.

Another SDK example is [WS2812 LED light strips](https://github.com/raspberrypi/pico-examples/tree/master/pio/ws2812). These are connected
in serial and you need to send an extremely specific set of highs and
lows to to set possibly hundreds of lights to the correct color. The
exact algorithm is:

* Send high signal followed by low.
* To send 0, go high for 0.35 uSec and low for 0.8 uSec.
* To send 1, go high for 0.7 uSec and low for 0.6 uSec.
* All signals expected to have +/- 150ns accuracy.
* Repeat 100s or thousands of times to set whole light strand.

We're certainly not easily bit-bashing that! I've actually tried to do
this for a single WS2812 LED on an under-powered 16 Mhz processor
using `NOP` commands to get the timing exactly right, and it was just
plain impossible to get accurate timing. But since you can set and
independent frequency for your PIO controller, and calculate the exact
time it takes for each instruction to execute, since it's one clock
cycle, it's really easy to get that timing dialed in.

But that's enough with the SDK examples.

## My PIO based stepper clock signal

What I want to do is drive a square wave generator to spin the stepper
motors. Depending on both the speed in mm per second I want to go, and
the mm per step, I can calculate an exact clock frequency. I can also
immediately detect a stall because we have an instruction `JMP PIN` that will
immediately respond to a pin going high in the code.

I have 4 registers to work with:

* OSR - Output shift register - Send data from normal memory to PIO.
* ISR - Input shift register - Send data from PIO to normal memory.
* X - scratch register
* Y - scratch register

This isn't much but it'll do.

I can send two bytes to the PIO:

* Number of steps.
* Number of clock cycles to wait to achieve the proper frequency.

This is a little different than the PIO wants. Remember I said it's
optimized to turn bytes directly in to GPIO, and GPIO directly in to
bytes. Here I'm sending intermediate values. But I am able to work
within the confines of the minimal provided assembly language to get
what I want.

Things are also a little complicated because I didn't think to put the
step pins from the left and right motors next to each other. The PIO
can deal with up to 4 pins, but wants them to be sequential. Luckily
the language includes a 'side pin' feature for cases like this.

I also have a different pin for stall detection, but the 'jump pin' is
also treated as a different bank of pins. It is a problem that I can
only test one pin so I'll need to fix that in hardware with an OR gate
later, so either the left or right motor stalling will abort the
code. For now I'll just pick one.

The basic algorithm:

* C program pushes number of steps and pre-calculated timing.
* PIO waits until it receives both.
* PIO goes in to a loop making a square wave, waiting pre-calculated
   number of instructions after both setting pin High and then low.
* PIO does another loop consuming number of steps.
* If the stall detection crashes we exit both loops.
* PIO sends the remaining number of steps (-1 if done, X if stalled)
  so the C program knows how far we actually moved, and if we completed
  the requested movement safely.

Here's a quick listing of the code. You'll need some familiarity with
assembler to follow along. Here are some specific PIO instructions to
help you along:

* `pull block` grabs data that the main program put in to the OSR,
   waiting for the data.

* `out y, 32` copies 32 bits from the OSR to the scratch y register.

* `pull noblock` an important hack. Try to grab data for the OSR, but
  if it's not there use whatever is in the X register. This
  effectively allows me to save the X register for reuse later.

* `set pins ...` update GPIO pins with values, optionally using the
  'side pins' I needed due to my pin assignment.

* `jmp x-- lp1` Decrement the register, jump UNLESS register was 0
  then fall through.

* `jmp pin lp0` Jump only if the jump pin has transitioned from low to
  high, else fall through.

```asm
;
; Drive stepper motors with PIO so the clocks are consistent and on time.
;
; Push in a number or steps to take, and the number of cycles to burn
; to get the correct frequency, then pull out the number of remaining steps
; so we can see if we stalled.

.program step_both
.side_set 1 opt

.wrap_target
    pull block             ; Get number of steps
    out y, 32
    pull block             ; Get clock cycles to burn to obtain correct frequency

lp0:
    out x, 32              ; save to x
    pull noblock           ; copy x back in to OSR to use each loop
    set pins, 1 side 0x1   ; Clock ON
lp1:
    jmp x-- lp1            ; Delay for (x + 1) cycles, x is a 32 bit number
    out x, 32              ; grab saved copy of burns
    pull noblock           ; copy x back to osr
    set pins, 0 side 0x0   ; Clock OFF
lp2:
    jmp x-- lp2            ; Delay for the same number of cycles again
    jmp pin lp3		   ; Abort if we report stall
    jmp y-- lp0            ; count as one full cycle
lp3:

    mov isr, y             ; Move remaining cycles in to isr
    push block		   ; Send off to main program
.wrap                      ; Wait for next set of instructions
```

Then all we need to kick things off in C:

```c
uint32_t step_clocks_for_frequency(uint frequency) {
  uint32_t clocks = (clock_get_hz(clk_sys) / (2 * frequency)) - 11; // 11 to account for control clock cycles
  return clocks;
}

void step_x_times(PIO pio, int sm, uint steps, uint frequency) {
  pio->txf[sm] = steps;
  pio->txf[sm] = step_clocks_for_frequency(frequency);

}
```

And to get the result to determine if we stalled:

```c
int32_t remaining_ticks = pio_sm_get_blocking(pio, sm);
```

Now we have an extremely well timed square wave that will look perfect
on an oscilloscope! Better than anything we could just do directly in
C. For now we block waiting for results. In the future we can run some
minimal housekeeping code in the foreground while waiting for results.

# Next Steps

Now I'm finally at the point where I can easily explore the can
crusher as a full unit. I can test the entire system quickly and see
what's going on. As expected there are several problems with the
initial design. There always are. The biggest problems are:

* There seem to be alignment problems as the motors stall more easily
   as the platform gets closer to the steppers.

* We're not getting nearly enough power to crush cans easily.

* When the system is stressed the motors can get out of sync, and move
  each side of the crushing platform far enough apart that things seize
  and I can't manually reset the plate without disassembly.

It seems like all of the problems are in the mechanical design. I'll
focus on upgrades with that next.

***********************

*All cad files, electronic files, source code, etc, referenced in this
 post series is available on [my github
 page](https://github.com/grant-olson/can-crusher).*