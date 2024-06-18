---
title: Reversing the Tek 2465 oscilloscope firmware
---

The Tektronix 2400-series analog oscilloscopes are arguably the best portable analog scopes ever built.
I have two scopes from the series, a 2465 and a 2467, the latter of which is my favorite for general
spelunking.
The 2467 has the counter-timer (CTT) option, which is quite handy, as it provides a counter and all
kinds of timing functions.
However, there is a bug in the firmware, whereby the on-screen display brightness seems to go to max whenever I bring up the CTT menu.

At one point, I decided to try and disassemble the firmware ROMs.
Perhaps I'd be able to find and fix the bug, as it seems Tektronix is no longer accepting
bug reports nor user complaints for these scopes.

This has turned into a bit of an odyssey, and this site exists to document some of my
travails.
Perhaps you will find it useful, or if not, at least amusing.
