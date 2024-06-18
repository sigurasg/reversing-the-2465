---
title: "Disassembling the 2465"
date: 2024-06-18
---

# Ghidra all the things!

When I first got it into my mind to reverse the 2465 firmware, I felt 
[Ghidra](https://www.ghidra-sre.org/) was the obvious tool to use,
as Ghidra is a powerful *free* reverse-engineering tool.

So I installed the latest version of Ghidra at the time.
I loaded up one of the ROM images and looked for an MC6800 disassembler.
Ghidra didn't offer the MC6800 processor, but it did have the 6805, so I tried
that.
This didn't produce promising results, and as I recall the disassembler quickly
ran into instructions it couldn't handle.

Naively, I had figured the 6805 must be a close relative of the MC6800, but alas
that's not true.
The 6805 has a very different architecture than the MC6800, and while the two share
a few opcodes, that's effectively where the similarity ends.

# Write an MC6800 language spec

Thankfully Ghidra's disassembler is "easily" extensible through "language specs",
which are written in a language called
[SLEIGH](https://fossies.org/linux/ghidra/GhidraDocs/languages/html/sleigh.html).

SLEIGH is pretty cool, as a language spec allow Ghidra to transform the
disassembled code to P-Code, which allows Ghidra to reason about what the code
does.
This in turn enables Ghidra's decompiler, which often does a good job of
summarizing long assembly routines to more concise C-like code.

So around November 2021 I set about writing an MC6800 language spec.
There already existed an MC6809 language spec that was quite good, so much of the
initial work was to pare down the MC6809 language spec to the subset that
MC6800 implements.
There are however considerable differences between the two, notably the opcodes
in the range 0x00-0x20 are all different (IIRC).

Around March 2022 I felt the MC6800 language spec was good enough for wider
use, so I submitted a pull request (PR) to the Ghidra project.
I got some initial feedback, and I thought the spec would soon be merged into
the public Ghidra release, but alas this did not happen.
After the initial exchange the Ghidra team went silent on my PR, which is still
not merged (as of June 2024).

# Write a Ghidra plugin

For one reason or another, I wasn't very motivated to work on the Ghidra reversing
for a while. In the meantime, however, I did write a
[MAME emulator](https://github.com/sigurasg/mame/tree/tek2465) for the 2465.
More on that later.

Around April 2024 my interest in this was again revived, due to some fun hacking
that was going on in the [TekScopes](https://groups.io/g/Tekscopes) forum.
So I took a critical look at how to use Ghidra on the 2465, and realized that there
were some problems.

## Problems 

* Since the MC6800 language spec did not ship with Ghidra, it was not terribly
  easy to use.

  Basically you had to either build Ghidra from the sources in my Github repository,
  or else you had to copy the language spec into an existing Ghidra installation.
  Still, there were some people who found the language spec and got some use out of it.

* A remaining problem with the 2465 reversing was that it's quite tedious to create a
  new project around a set of ROM files.

* A larger problem is that the Ghidra disassembler and decompiler do not understand
  the ROM banking that occurs in the 2465A and 2465Bs.

  This makes reversing tedious as the disassembly does not propagate across banks
  during auto-analysis.
  Even manually disassembling the other banks still leaves the call tree disconnected
  or wrong.

## We haz solutions!

To solve these problems in one fell swoop, I decided to write a
[Ghidra Plugin](https://github.com/sigurasg/GhidraTek2465).

* The plugin embeds the MC6800 language spec.

  Anyone can now disassemble the MC6800, simply by installing the plugin, even if they
  have no interest in the Tek 2400 family of analog scopes.

* The plugin has a loader for the 2465 family ROMs.

  This makes it a breeze to create new projects around a set of ROMs, as the loader sets
  up the correct memory map and populates it with some useful types.

* The plugin has an analyzer that understands the banking.

  This propagates the disassembly across banks, though it may need running the
  auto-analysis a couple of times before it converges to all reachable code.
  The analyzer also marks the banking functions as thunks to their destinations,
  which allows the analysis to build a call tree that reaches across banks. 

## Progress

Using the plugin I've been able to make some headway, though reversing is always
going to be a tedious process.
After walking through the power-on-self-test (POST) code, I was able to locate some
interesting functions, notably a function that writes strings to the
on-screen-display (OSD).
This in turn makes it easy to locate the strings that are written to the OSD, though
as detailed in the previous post, these are fairly inscrutable as-is.

More on locating and decoding the OSD strings in a later post...
