---
layout: post
title: "Be vewy vewy quiet, I'm hunting strings"
date: 2024-07-03
author: Sigurður Ásgeirsson
---
After I'd written the
[MC6800 language spec](https://github.com/sigurasg/GhidraTek2465/blob/main/data/languages/mc6800.slaspec)
and the
[Ghidra Plugin](https://github.com/sigurasg/GhidraTek2465?tab=readme-ov-file#ghidratek2465),
and after decoding the [OSD alphabet]({{site.baseurl}}{%post_url 2024-06-17-osd-alphabet%}),
it was time to go hunting for OSD strings.
Walking through the power-on-self-test (POST) routines is relatively straightforward and
eventually leads to code that writes to the OSD.

From there it's largely a matter of a little bit of automation.

# Automating the OSD string search

Because of the [limitations of the MC6800]({{site.baseurl}}{%post_url 2024-06-18-mc6800-limitations %}),
it turns out that the pointer to each OSD string is written to a global variable,
which in turn is a "parameter" for the function that writes the OSD.
It also turns out that the MC6800 offers only one good way to load a pointer into a global:
```
LDX #OSD_STRING_ADDRESS
STX OSD_STRING_POINTER
```

Apparently it further transpires that the Tektronix engineers maintained this
global variable location as a stable interface throughout the 2465-series, so the same 
[Ghidra script](https://github.com/sigurasg/GhidraTek2465/blob/main/ghidra_scripts/FindOSDStrings.java)
will serve to find most OSD strings in all firmware ROM versions.

I assume that options were given service functions in the buffer board ROM to use
to implement OSD display, keyboard scanning, etc, and since parameters must be
passed in globals, the address of this global has to be stable as well.

Knowing this it was easy to write a Ghidra script that 
  * walks back from all references to the global in question,
  * checks for the `LDX/STX` pattern,
  * decodes the OSD strings,
  * types the strings,
  * adds the decoded string as a comment on the string block.

Since automation is free once written, I also had it create references from the
`LDX` instructions to the strings, as well as bookmarks for the strings themselves.
This makes it easy to locate each string and to walk back to the function or
functions that use it through Ghidra's references.

# What do we find?

After all this hoopla here are the strings that turned up in the latest version of
the 2465B early ROMs (160-5370-10/160-5371-10).

| `LIMIT` |
| `RISE` |
| `TIME` |
| `FALL` |
| `INTERVAL` |
| `FROM.CH` |
| `TO.CH` |
| `..../5/0/Ohm../O/V/E/R/L/O/A/D....` |
| `CH2.DLY.-.TURN.delta` |
| `DISABLED` |
| `.deltaV` |
| `CH1` |
| `CH2` |
| `PERIOD` |
| `FREQUENCY` |
| `Hz` |
| `deltaREF.HRS...delta.PWR...PUSH.MAG.10/1` |
| `HRS.ON` |
| `HIGH` |
| `LOW` |
| `DUTY` |
| `CONNECT.SIGNAL.TO.CH2` |
| `CONNECT.SIGNAL.TO.CH1` |
| `PWR.ON/OFF` |
| `mC/SQ.CM.` |
| `C/SQ.CM.` |
| `FLDS-NO.CUR.W//DLY` |
| `.RATIO` |
| `CH1.VAR.CH2.POS` |
| `.<..1%` |
| `.>99.9%` |
| `DC.BALANCE.IN.PROGRESS` |
| `.dt` |
| `.1/dt.` |
| `/DLY` |
| `PHASE` |
| `.DEG` |
| `DEBUG.` |
| `DISABLED` |
| `ENABLED` |
| `AT....SECONDS` |
| `CAL` |
| `FAIL` |
| `LOOP` |
| `CYCLE` |
| `XX` |
| `DIAGNSTIC..PUSH.A/B.TRIG.TO.EXIT` |
| `VOLTS` |
| `FREQ` |
| `WIDTH` |
| `RISE-t` |
| `FALL-t` |
| `TIME` |
| `.MEASUREMENT.IN.PROGRESS` |
| `SAVE.FUNCTIONS.DISABLED.........` |
| `SAVE.1-8.DIRECTLY...NAME:.......` |
| `...............TURN.delta.TO.SELECT.` |
| `REPLACEINSERT.DELETE.STEP.` |
| `INSERT.DELETE.STEP.` |
| `DELETE.STEP.` |
| `MEAS` |
| `NAME` |
| `STEP.` |
| `BEGIN` |
| `END..` |
| `TURN.delta.CONTROL` |
| `SHUTDOWN.WARNING` |
| `VERT.CENTER.GAIN` |
| `ADJ.delta` |
| `SET.R949.<Z.ADJ>.` |
| `FOR.GRID.DRIVE.` |
| `TURN.delta.TO.MATCH.GRID.DRIVE:...V` |
| `SET.GRID.BIAS.TO.MATCH.DOT.INTEN` |
| `1..TRACE.ROTATION.AND.Y-AXIS` |
| `2..FOCUS,.ASTIG,.AND.GEOMETRY` |
| `CONNECT.8.DIV,.158` |
| `CONNECT.6.DIV,.10` |
| `.MHz.SINE.WAVE` |
| `SET.HIGH.DRIVE.FOCUS` |
| `SET.MCP.BIAS.SO.ZERO.CROSSINGS` |
| `ARE.JUST.VISIBLE.WITH.20.FT.CD` |
| `ARE.FLASHING.SINE.WAVES.CLEARLY` |
| `VISIBLE.WITH.20.FT.CD?` |
| `WITH.A.DIM.TRACE,.ADJUST.Z-AXIS` |
| `TRANSIENT.RESP.FOR.UNIFORM.INTEN` |
| `DIM.DOT.IN.NORMAL.ROOM.LIGHT?` |
| `TRACE.INTEN.OFF,.NO.DOT.VISIBLE?` |
| `SET.HORZ` |
| `.DYNAMIC.CTR.FOR.MINIMUM` |
| `HORZ.SHIFT.WITH.INTENSITY.CHANGE` |
| `SET.VERT` |
| `VERT.DEFLECTION.OF.INTEN.ZONE` |
| `PK-PK` |
| `POS-PK` |
| `NEG-PK` |
| `AVG` |
| `CH` |
| `.....2V.` |
| `.500mV.` |
| `X1.X10.HRZ.CTR` |
| `ADJ.TRANS.RESP` |
| `CH1.PROBE.TO.TP800.ON.MAIN.BD` |
| `CH2.DLY.ADJUST.` |
| `ENABLED` |
| `DISABLED` |
| `COUPLING.UP.CLEARS.SAVED.SETUPS` |
| `ENABLE.SAVE.AND.SEQUENCE-CHANGE.` |
| `ENABLE.SAVE.1.-.8,.NO.SEQ-CHANGE` |
| `DISABLE.SAVE.AND.SEQUENCE-CHANGE` |
| `POWER.UP.TO.` |
| `SETUP.1` |
| `POWER.DOWN.SETUP` |
| `.DUTY` |
| `UNDER.LIMIT` |
| `MID-PT` |
| `SHUTDOWN.WARNING` |
| `AT....SECONDS` |
| `ENABLED` |
| `DISABLED` |
| `TURN.delta.CONTROL` |
| `DEBUG.` |
| `..READOUT.CAL..` |
| `NOISY.OR.APERIODIC.SIGNAL` |
| `ExCESSIVE.NOISE/ABBERATIONS` |
| `DC.OFFSET..TRY.AC.COUPLING` |
| `SMALL.OR.LOW.REP.RATE.SIGNAL` |
| `.%` |

# Next up

From here it should be relatively straightforward to e.g. locate
the calibration code as well the code that handles POST failures,
which makes further reverse engineering that much easier.

It is straightforward to reproduce this:
  * Install Ghidra.
  * Install the latest [GhidraTek2465 Plugin release](https://github.com/sigurasg/GhidraTek2465/releases).
  * Load up a pair of ROMs from TekWiki.
  * Run the auto-analysis a couple of times.
  * Run the [FindOSDStrings](https://github.com/sigurasg/GhidraTek2465/blob/main/ghidra_scripts/FindOSDStrings.java) script.
  * Profit!

Anyone who's interested in chasing this is welcome to 
[shoot me an email](mailto:{site.email}) and I can forward you the Ghidra project(s) I'm working on,
or I can create an account for you on my Ghidra server.
