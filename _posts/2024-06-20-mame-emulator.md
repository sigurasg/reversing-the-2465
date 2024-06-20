---
layout: post
title: "MAME Emulator"
date: 2024-06-20
author: Sigurður Ásgeirsson
---

Around December 2021 I had Ghidra disassembling MC6800 object code.
The language spec was however still broken in important ways and the disassembly
would derail pretty quickly.
I got to wonder if there might be an "easier way"; imagine if I could observe the
firmware running, trace and debug it.

At the time I knew nothing about MAME except that it existed.
After a quick look at the sources it looked quite promising and actually writing
an emulator looked straightforward, if perhaps tedious.

# The easy part

MAME already had an MC6800 CPU implementation as well as an implementation of the EAROM.
Emulating the basic memory map of the 2465 was a breeze, so getting the firmware to run
the reset routine and start plodding on was a matter of minutes.
The CPU has only a few IO registers, and it wasn't too hard to map those out to simulate
the DAC and the other IO ports.

From there it gets a little trickier, as the CPU mostly communicates with its
"pheripherals" through serial communications.
Still, it only took me from mid-December to Christmas to get the on-screen-display (OSD)
rendering.
It was a nice "Eureka" moment when I first saw the scope displaying a power-on-self-test
failure message.

I do however remember I was stuck on the calibration jumper for a while.
It turns out if `J501` is in the "neither" position, the firmware goes into some kind of
diagnostic loop mode, where it never writes to the OSD, so it was important to simulate
the jumper in one position or the other.

Plumbing up the EAROM and various diagnostic bits allowed the firmware to pass the
power-on-self-tests (POSTs) up to the 05 tests.
Implementing basic emulation of the various Tektronix hybrids was easy enough, as from
the CPU's point of view they simply contain shift registers.

# The hard part

The harder part of the emulator was to simulate the scope front panel and switch matrices.
The 2465, more so than other scopes of the family, has quite the involved switch matrix.
This is due to the vertical V/DIV and horizontal SEC/DIV controls, which are all gray-code
encoded, and it turns out this kind of throws the MAME input machinery for a loop.

There's also the fact that some of the controls are just complicated.
In particular the A/B SEC/DIV controls are interlocked, and the B SEC/DIV control can be
pulled out in addition to rotated.

I have a feeling there has to be a better way to implement this than what I've done so far,
though I really don't know.

# Current state

This video shows the emulator in action a while back.
<iframe width="560" height="315" src="https://www.youtube.com/embed/3GvP1uClw5A?si=tuCg972yVv8cFBGz" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The current state of the emulator is that it's largely functional in terms of the UI and
digital controls:
  * The OSD works.
  * Cursors work.
  * The front panel controls work (if used carefully).
  * Front panel LEDs work.
  * The calibration and ID jumpers work. 
  * Relays click at appropriate times.

All the Tektronix custom hybrids have some sort of representation in the emulation, and
it's possible to inspect their state as e.g. V/DIV or SEC/DIV controls are exercised.

# Future work

### Signal handling

What's primarily missing is any kind of signal handling, which is required to get
past the 05 POSTS.

To do that it's necessary to:
  * Reverse engineer the Display Sequencer (DS) state machine.
  * Emulate the trigger hybrids.
  * Emulate the sweep hybrids.
  * Simulate an input signal.
  * Simulate attenuators and preamps.
  * Render the signal to the screen.

This is not insurmountable and I have a spare DS I am intending to breadboard with a
'duino at some point to extract its secrets.

### Support the later models

As-is the emulator only covers the basic 2465, but in principle it wouldn't be too hard to
cover the later models.
There isn't a whole lot of extra hardware from the 2465 to 2465A, mainly I think the memory
map adds ROM banking.
The 2465B adds some hardware for measurement, which is likely not too hard to emulate, though
I don't know much about it.

All of the later models have quadradic encoders for the SEC/DIV and V/DIV controls, so they
should be easier to handle than the 2465.
It's still fairly painstaking work to write the front panel layout and plumb up the switch
matrixes.