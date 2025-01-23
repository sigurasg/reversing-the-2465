---
layout: post
title: "2465 Calibration"
date: 2025-01-22
author: Sigurður Ásgeirsson
---

The sweep calibration process in the 2465A/B scopes is interesting, and it seems
to cause confusion for everyone the first time they go through the process.
This post is intended to shed light on the intent with the sweep calibration
steps, and how sweep calibration actually works.

This post is not directly related to reversing the firmware in the 2465-series,
though eventually the details of how calibration works might be verified by looking
through the firmware.

# Background

The 2465 series scopes are drive-by-wire, where there is an MPU that controls most
of the hardware.
The hardware is generally controlled in one of two ways:
  1. Through bitstreams that are shifted into and sometimes out of the various hybrids.
  2. Through analog voltages generated on the A5 board.

In some cases, both modes are involved.

## Sweeps

In the case of the sweep hybrids, both a serial bitstring and analog voltages are involved.

Each of the two sweep hybrids in the 2465-series contains a pair of dies, a
[203-0214-90 Sweep Integrator](https://w140.com/tekwiki/images/c/c9/Tek-Made_Integrated_Circuits_Catalog.pdf#page=295) a
[203-0231-90 Sweep DAC](https://w140.com/tekwiki/images/c/c9/Tek-Made_Integrated_Circuits_Catalog.pdf#page=323) plus
a handful of resistors and capacitors.
The Tek-Made catalog contains what appears to be
[a schematic](https://w140.com/tekwiki/images/c/c9/Tek-Made_Integrated_Circuits_Catalog.pdf#page=301)
of the entire hybrid.

The sweep hybrid implements a conventional constant current slope generator with switchable
current ranges and timing capacitors.
What's different from older analog scopes is that there is no mechanical switching, all the switching
is implemented electronically.

### Sweep slope

To set up a particular sweep slope, a 7 bit serial bitstring is shifted into the Sweep DAC die, which
selects the coarse-level current generator and the timing capacitor.
The slope is then selected or adjusted with a `timing reference` analog voltage from the A5 board,
which is one of `A_TIM_REF` or `B_TIM_REF` for the A or B sweeps respectively.
Since the sweep current is linearly related to the `timing reference` voltage, so is the sweep slope.
The sweep calibration process consists of setting up a series of calibrated sweep slopes at particular
sweep speeds, but since the slope is under MPU control through the `timing reference` voltages,
the 2465-series has calibrated sweep speeds even with the SEC/DIV `VAR` control out of detent.

### Delay comparator

Each sweep hybrid also contains a fast comparator that compares the sweep ramp to a `delay reference`
analog voltage on the `DR` pin of the sweep hybrid.
This is primarily used to initiate delay sweeps, but is also used in sneaky ways during calibration.
The A5 board generates two delay reference signals, namely `DLY_REF_0` and `DLY_REF_1`.
Both of those can be routed to both sweep hybrids under control of the Display Sequencer.
Under dual delay sweeps, the two voltages are alternately routed to the `DR` pin by logic in the
Display Sequencer.

# Calibration

## Principle

The principle of sweep calibration in the 2465 series is that using a human as a comparator,
against a calibrated timing signal, a series of calibrated sweep slopes are configured.

### Pre-flight checking

The first step in the sweep calibration is to to verify that the `DLY_REF_O` and `DLY_REF_1`
signals are in concordance.
This is step `o)` in the 2465B calibration sequence, where both delay markers are aligned
on the same timing mark and the B-sweep magnified timing markers are aligned.
It's a fair guess that if the DAC codes used to set the two voltages are not near-identical,
then the dreaded `LIMITS` error will occur.

If you look at how those two signals are generated on the A5 board, you'll see that these
two signals are straight up buffered from the sample-and-hold MUX.
This is - I believe - not a trivial detail, as if these were offset or amplified, there
would be component differences to deal with.
This would at minimum require at least one more calibration step to find the relative slope
and/or offset differences between the two delay signals, and at the same time would only
allow limit checking on the slope/offset values.

### Reference slope

The next significant step (step `r)` in the 2465B calibration sequence) involves setting
up the first reference slope, which *after* CRT adjustment will be $$100\mu \ s/DIV$$.

To do this, the calibration firmware initiates a `strawman` sweep that has a *voltage* slope
fairly close to the intended *voltage* slope.
The technician is then directed to select the 2nd and 10th timing markers on the A-sweep
and to superimpose the timing markers on the B-sweep.
Once this is done we have a situation where $$\Delta d = d_1 - d_0$$, the difference between
the calibrated voltages $$d_0$$ = `DLY_REF_O` and $$d_1$$ = `DLY_REF_1` represents a
well-defined time $$ \Delta t$$.

TODO: Talk about steps t-w, looks like they just verify `r)`?

The slope can now be computed simply as $$s_s =\Delta d/\Delta t \ V/s $$ - note
that this and all subsequent slopes are voltage slopes.

It is now possible for the firmware to either use the `strawman` slope as reference, or
more likely to compute the `timing reference` needed for the intended reference voltage
slope $$ s_r = cV /100 \mu s $$.

Either way the reference slope can be assumed to
be $$ s_r = c/100 \mu \ V/s = c/10^{-6} \ V/s =  s_r = c*10^6 \ V/s $$.

What's the bet that $$ c = 1V $$?


### Horizontal CRT calibration

The next step (step `aa` in the 2465B calibration sequence) involves adjusting horizontal
gains to align the cursors, then the CRT to the timing markers under the reference slope.

After this step concludes, one horizontal DIV is has been calibrated to equal $$c \ V$$,
so the the CRT has now been calibrated to a known horizontal voltage, e.g. $$ 1DIV=c \ V$$
meaning that $$1 \ V = 1/c \ DIV$$.
It follows that any slope $$ s = k \ V/s = k/c \ DIV/s $$, e.g. it is now possible to
trivially convert any slope to a sweep speed.

