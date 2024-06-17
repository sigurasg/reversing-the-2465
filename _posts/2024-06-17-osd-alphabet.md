---
title: "On Screen Display (OSD) Alphabet"
date: 2024-06-17
---

# Motivation

Strings can often provide solid clues or anchors when reversing code, as the strings referenced
often give away the purpose of a piece of code in human-readable terms.
The problem with the 2465 readout system in this respect, is that the strings are all written
in the readout system's alphabet.
Locating the OSD strings and converting them to ASCII would greatly help the reversing effort.

# Method

In order to decipher the OSD alphabet it's necessary to understand how the readout system works.
Thankfully the Theory of Operation section in the service manuals has a solid explanation of how
the readout system renders the of the character RAM to the CRT, through the contents of the
character ROM.

Once this is understood, it's possible to render the data in the character ROM.

TODO(siggi): Writeme!
