---
layout: post
title: "2465 Sweep Calibration"
date: 2025-06-29
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

The 2465-series scopes are drive-by-wire, where there is an MPU that controls most
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

To set up a particular sweep slope, first a 7 bit serial bitstring is shifted into the Sweep DAC die.
This selects the current generator that decides the coarse-level current as well as the timing capacitor involved.
The slope is then selected or adjusted with a `timing reference` analog voltage from the A5 board,
which is one of `A_TIM_REF` or `B_TIM_REF` for the A or B sweeps respectively.
Since the sweep current is linearly related to the `timing reference` voltage, so is the sweep slope.

The sweep calibration process consists of setting up a series of calibrated sweep slopes at particular
sweep speeds, but since the slope is under MPU control through the `timing reference` voltages,
the 2465-series has calibrated sweep speeds even with the SEC/DIV `VAR` control out of detent.

### Delay Comparator

Each sweep hybrid contains a fast comparator that compares the sweep ramp to a `delay reference`
analog voltage on the `DR` pin of the sweep hybrid.
This is primarily used to initiate delay sweeps, but is also used in ingenious ways during sweep calibration.
The A5 board generates two delay reference signals, namely `DLY_REF_0` and `DLY_REF_1`, hereafter
named $$d_0 $$ and $$d_1 $$.
Both of those can be routed to either sweep hybrid under control of the Display Sequencer.
Under dual delay sweeps, these two voltages are alternately routed to the `DR` pin by logic in the Display Sequencer.

# Sweep Calibration

## Principle

The principle of sweep calibration in the 2465-series is that a series of calibrated sweep
slopes are configured against a calibrated timing signal.
The delay comparators in the sweep hybrids are used to create a target slope for the
technician to match, which allows sweep slope calibration without regard to CRT calibration.
In this process, the technician serves only as a comparator, using his or her mark one eyeball
to compare timing markers on the CRT.

Independent to the sweep slopes, the CRT is calibrated to a deflection factor of $$ d_{CRT} = 0.25 \ DIV/V$$.
This is done by calibrating the CRT gains against the horizontal cursors.
The cursor positions are controlled with voltages from the A5 board, which means they can each be
set to a highly accurate voltage.

Once each sweep $$i$$ has been calibrated to specific $$ c_i = V / s $$ value, and the CRT
has been calibrated to $$ d_{CRT} = 0.25 \ DIV/V $$, the sweeps will generate a calibrated
speed of $$ s_i = c_i \ V/s * 0.25 \ DIV/V = 0.25c_i \ DIV/s$$.

### Details

For each target slope, the calibration firmware creates a target for the technician to aim for,
by setting $$\Delta d = d_1 - d_0 $$ to a voltage difference that matches the targeted
time delta. So, as an example, at a sweep speed of $$v = 100 \ {\mu s}/DIV $$, the targeted
slope is:

$$slope = 1/(v \ DIV/s) \ (d_{CRT} \ V/DIV) => $$

$$ slope = (d_{CRT} \ V/DIV)/(v \ DIV/s) => $$

$$ slope = d_{CRT}/v \ V/s => $$

$$ slope = 0.25/100 \ 10^{-6} \ V/s => $$

$$ slope = 2500 \ V/s $$

To target this slope, the calibration firmware sets the two delay references to a
difference of $$8 d_{CRT} $$, which works out to
$$ \Delta d = 8 \ DIV * 0.25 \ V/DIV = 2 \ V $$.

The technician is then instructed to highlight the 2nd and 10th timing markers and
to superimpose them in the B-sweep (or on a bench scope).
This alignment is attained by adjusting the two timing references, in order to move
the target markers into view, then adjusting the sweep slope to overlay them
on the CRT.
This overlaying is done with the B-sweep (or a bench scope) in order to magnify the
timing makers, which allows a high degree of accuracy in aligning them.

Once the technician has aligned the two timing markers as instructed, the target slope
has been attained with great accuracy.
Since the $$d_0 $$ and $$d_1 $$ signals have pretty high resolution (somewhere in excess
of 350 steps/DIV), and because the B-sweep (or the bench scope) have a magnifiying
effect, the target slope alignment is much more accurate than the old timey practice
that involved aligning timing markers to a CRT.

## Calibration steps

### Prerequisites

It's important to validate all power supplies, and to perform the DAC calibration before
attempting the sweep calibration.
If the power supplies are no good, then the calibration is unlikely to succeed.
If the DAC is out a little, the calibration may succeed, but any future calibration is
likely to need a full redo.
If the DAC is out a lot, the calibration will likely fail with the dreaded `LIMITS`
error.

### Preflight checking

The first step in the sweep calibration is to to verify that the $$d_0 $$ and $$d_1 $$
signals are in concordance.

This is step `o)` in the 2465B calibration sequence, where both delay markers are aligned
on the same timing mark, and the B-sweep magnified timing markers are superimposed.
It's a fair guess that if the DAC codes used to set the two delay reference voltages are
not near-identical, then the dreaded `LIMITS` error will occur.

It is noteworthy that those two signals are staight-up buffered from the DAC on the
A5 board.
This is not a trivial detail, as if these were offset or scaled, component differences
in the op-amp circuits could cause offset or slope differences between the two.
This would at minimum require at least one more calibration step to find the relative slope
and/or offset differences between the two delay signals, and at the same time would only
allow limit checking on the slope/offset values.

### Reference slope

The next significant step (step `r)` in the 2465B calibration sequence) involves setting
up the first reference slope, which *after* CRT adjustment will be $$100 \ {\mu s}/DIV $$.

To do this, the calibration firmware initiates a sweep that has a slope fairly close to
the expected end result. In the case of prior calibration, this slope will be the prior
calibrated slope.
A target slope is then set, using the delay references as diDeltascussed above.

The technician is then directed to use the `DLY POS` and $$\Delta $$ controls to highlight the
2nd and 10th timing markers on the A-sweep and to superimpose the timing markers on the B-sweep.
One of those controls adjusts the `A_TIM_REF` signal, which in turn adjusts the slope of
the on-screen sweep. The other control offsets the timing markers by adjusting the $$d_0 $$
and $$d_1 $$ voltages by the same amount.

Once this is achieved, the target slope has been set.

Steps `t)` through `v)` test the linearity of the sweep hybrid by dropping the slope to
$$ 300 \ {\mu s}/DIV $$ and comparing the timing markers agains that sweep.

Steps `w)` through `x)` re-test the $$ 100 \ {\mu s}/DIV $$ sweep slope.

### Horizontal CRT calibration

The next step (step `aa` in the 2465B calibration sequence) involves adjusting horizontal
gains to align the cursors and timing markers to the CRT.

Note that the cursors are derived from the DAC voltages the same way the timing references,
so the calibration firmware can set the cursors to specific locations that match up with
graticule markers with the same precision that defines the sweep slopes.
This allows highly accurate calibration of the gain and offset for the CRT
with respect to the cursors, and hence the sweep slopes.
It's worth noting that the 10X gain is set to the timing markers, which is the only case
where the CRT is calibrated directly to timing markers[^1].

After this step concludes, the horizontal 1X CRT deflection factor has been calibrated to
$$d_{CRT} = 0.25 \ V/DIV$$, centered to the mid-range of the sweep extents.
It then follows that any sweep speed $$v \ DIV/s $$ can be converted to a calibrated slope
by $$slope = 1/v * d_{CRT} $$.

The 10X horizontal deflection factor has also been calibrated to
$$d_{CRT 10X} = 25 \ mV/DIV$$.

### More A-sweep slopes

Steps `bb)` through `ff)` calibrate the remaining A-sweep slopes by the method
described above.

### B-sweep slopes.

Steps `gg)` through `jj)` calibrate the B-sweep slopes by the same principle[^1] as
the A-sweep slopes are calibrated.

[^1]: The calibration proceedure for the origional 2465 calibrates the
    B-sweep in the more conventional manner, e.g. by by aligning timing markers
    to the graticule. As of the 2445A/2465A/2467, the B-sweep is calibrated with
    the aid of a second scope, which is the proceedure discussed below.

The difference is that the scope itself does not have any hardware to display
magnified timing markers for the B-sweep.
In order to overcome this limitation, the output of the B-sweep hybrids'
delay comparator is routed to the B-sweep gate of the scope.
This is done with dedicated circuitry whose sole purpose in life is to
aid calibration.
This signal routing allows the use of a second scope to provide the timing marker
magnification, which allows the B-sweep to be calibrated with the same accuracy as
the A-sweep.

These steps are fairly confusing to the uninitiated[^2].
The principle is the same as with the A-sweeps, but as the bench scope is unable
to highlight the interesting timing markers, it's important for the technician
to have a solid mental image of what's going on, which is this:
1. The B-gate outputs alternate timing references for the bench scope to trigger on.
2. The technician needs to adjust the offset and sweep slope to align two timing
   markers on the bench scope's CRT.
3. The principle involed is the same as with the A-sweeps, but if one
   or both timing markers are off the CRT then it may be hard hard to orient.
4. The key thing to note is that that the `DLY POS` control "slides" across
   the timing markers, while the $$\Delta $$ control adjusts their distance.

[^2]: I'm speaking from personal experience here, I found this part of
    the calibration process quite confusing when I calibrated my 2467.

If both markers are off the CRT, it might be helpful to slow down the sweep
speed on the bench scope to get a better sense for what's happening.
Once both markers are positioned towards the front of the slower sweep, it's
time to switch to the specified sweep speed to align them.
