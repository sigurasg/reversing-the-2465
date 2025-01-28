---
layout: post
title: "2465 Sweep Calibration"
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

### Sweep Slope

To set up a particular sweep slope, a 7 bit serial bitstring is shifted into the Sweep DAC die, which
selects the coarse-level current generator and the timing capacitor.
The slope is then selected or adjusted with a `timing reference` analog voltage from the A5 board,
which is one of `A_TIM_REF` or `B_TIM_REF` for the A or B sweeps respectively.
Since the sweep current is linearly related to the `timing reference` voltage, so is the sweep slope.

The sweep calibration process consists of setting up a series of calibrated sweep slopes at particular
sweep speeds, but since the slope is under MPU control through the `timing reference` voltages,
the 2465-series has calibrated sweep speeds even with the SEC/DIV `VAR` control out of detent.

### Delay Comparator

Each sweep hybrid contains a fast comparator that compares the sweep ramp to a `delay reference`
analog voltage on the `DR` pin of the sweep hybrid.
This is primarily used to initiate delay sweeps, but is also used in sneaky ways during calibration.
The A5 board generates two delay reference signals, namely `DLY_REF_0` and `DLY_REF_1`, hereafter
named $$d_0 $$ and $$d_1 $$.
Both of those can be routed to both sweep hybrids under control of the Display Sequencer.
Under dual delay sweeps, the two voltages are alternately routed to the `DR` pin by logic in the
Display Sequencer.

# Sweep Calibration

## Principle

The principle of sweep calibration in the 2465 series is that a series of calibrated sweep
slopes are configured using a technician as a comparator against a calibrated timing signal.
The delay comparators in the sweep hybrids are used to create a target slope for the
technician to match, which allows sweep slope calibration without regard to CRT calibration.

Note that the calibrated sweep slopes all assume the same CRT deflection factor
$$d = c \ V/DIV $$, where $$c = 0.25 $$.
Once the first sweep slope has been established, the horizontal gains are adjusted to set the
CRT to this normalized deflection factor.

### Details

For each target slope, the calibration firmware creates a target for the technician to aim for,
by setting $$\Delta d = d_1 - d_0 $$ to a voltage difference that matches the targeted
time delta. So, as an example, at a sweep speed of $$v = 100 \ \mu s/DIV $$, the targeted
slope is

$$s = 1/v \ DIV/s \ d \ V/DIV => $$

$$ s = 1/(100 \ 10^-6) \ DIV/s \ 0.25 \ V/DIV => $$

$$ s = 1/(10^-4) \ DIV/s \ 0.25 \ V/DIV => $$

$$ s = 2500 \ V/s $$

To target this slope, the calibration firmware instructs the technician to highlight
the 2nd and 10th timing markers and to superimpose them in the B-sweep. It then
sets the two delay references to a difference of $$8 d $$, which works out to
$$\Delta d = 8 \ DIV * 0.25 \ V/DIV = 2 \ V $$.

Once the technician has aligned the two timing markers as instructed, the target slope
has been attained with a high degree of accuracy.
Since the $$d_0 $$ and $$d_1 $$ signals have pretty high resolution (somewhere in excess
of 350 steps/DIV), and because the B-sweep (or the bench scope) have a time magnifiying
effect, the target slope alignment is much more accurate than the old timey practice
that involved aligning timing markers to a CRT.

## Calibration steps

### Prerequisites

It's important to validate all power supplies and to perform the DAC calibration before
attempting the sweep calibration.
If the power supplies are no good, then the calibration is unlikely to succeed.
If the DAC is out a little, the calibration may succeed, but any future calibration is
likely to need a full redo.
If the DAC is out a lot, the calibration will likely fail with the dreaded `LIMITS`
error.

### Pre-flight checking

The first step in the sweep calibration is to to verify that the $$d_0 $$ and $$d_1 $$
signals are in concordance.

This is step `o)` in the 2465B calibration sequence, where both delay markers are aligned
on the same timing mark, and the B-sweep magnified timing markers are superimposed.
It's a fair guess that if the DAC codes used to set the two delay reference voltages are
not near-identical, then the dreaded `LIMITS` error will occur.

It is noteworthy that those two signals are staight up buffered from the DAC on the
A5 board.
This is not a trivial detail, as if these were offset or scaled, component differences
in the op-amp circuits could cause offset or slope differences.
This would at minimum require at least one more calibration step to find the relative slope
and/or offset differences between the two delay signals, and at the same time would only
allow limit checking on the slope/offset values.

### Reference slope

The next significant step (step `r)` in the 2465B calibration sequence) involves setting
up the first reference slope, which *after* CRT adjustment will be $$100\mu \ s/DIV $$.

To do this, the calibration firmware initiates a sweep that has a slope fairly close to
the expected end result. In the case of prior calibration, this slope will be the prior
calibrated slope.
A target slope is then set, using the delay references as discussed above.

The technician is then directed to use the `DLY POS` and $$\Delta $$ controls to highlight the
2nd and 10th timing markers on the A-sweep and to superimpose the timing markers on the B-sweep.
One of those controls adjusts the `A_TIM_REF` signal, which in turn adjusts the slope of
the on-screen sweep. The other control offsets the timing markers by adjusting the $$d_0 $$
and $$d_1 $$ voltages by the same amount.

Once this is achieved, the target slope has been set.

TODO(siggi): Discuss steps `t)` to `v)`.

### Horizontal CRT calibration

The next step (step `aa` in the 2465B calibration sequence) involves adjusting horizontal
gains to align the cursors and timing markers to the CRT.

Note that the cursors are derived from the DAC voltages the same way the timing references,
so the calibration firmware can set the cursors to specific locations that match up with
graticule markers with the same precision as defines the sweep slopes.
This allows highly accurate calibration of the gain and offset for the CRT
with respect to the cursors, and hence the sweep slopes.
It's worth noting that the 10X gain is set to the timing markers, which is the only case
where the CRT is calibrated directly to timing markers.

After this step concludes, the horizontal 1X CRT deflection factor has been calibrated to
$$d = 0.25 \ V/DIV$$ and the centered to the mid-range of the sweep extents.
It then follows that any sweep speed $$v \ DIV/s $$ can be converted to a calibrated slope
by $$s = 1/v * d $$.

The 10X deflection factor has also been calibrated to $$d_{10X} = 25 \ mV/DIV$$.


TODO(siggi):
  1. Rest of A-sweep calibration.
  2. Discuss B-sweep calibration.
