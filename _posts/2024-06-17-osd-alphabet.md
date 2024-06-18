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

# Deciphering

In order to decipher the OSD alphabet it's necessary to understand how the readout system works.
Thankfully the "Theory of Operation" (ToO) section in the service manuals has a solid explanation
of how the readout system renders the contents of character RAM to the CRT, through the contents
of the character ROM.

The ToO describes how the circuitry works in excruciating detail, but the gist of it is this:
1. A byte in the readout RAM selects a character to display.
   The start address of the character in character ROM is simply character value * 16.
2. Readout characters comprise a series of dots, where each is encoded into a byte.
   The X, Y coordinates of each dot are respectively encoded in 3 and 4 bits of the byte,
   which means each character can be as wide as 8 dots, but as tall as 16.
3. A dot with the MSB clear ends the character.

The way this is laid out is that some characters are designed to be used in pairs to make
a single large character.
As the end goal is to be able to convert a series of OSD bytes into a human-readable string,
these pairwise characters are considered a single character for our purposes.

After some experimentation, I wrote a relatively simple python script to convert the character
ROM to a bunch of PNG files.

Initially I assumed that "small" characters would be 8x8 while the dual "large" characters
would be 16x16.
This is however not true, there are several "small" characters that extend below the 8 row
boundary. All the small characters below are rendered to 16 rows, though few use the space.

# The alphabet

I wager that the organization of this alphabet is largely determined by the glyphs Tektronix
wanted to display.
Note that any character that needs more than 16 dots will consume the subsequent character
position, so there are several gaps in the character codes.
The first subsumed code is 0x03, followed by 0x07 and 0x0B.

It's also interesting to note that all of the digits occur 4 times in this alphabet, as they
appear large and small, as well as with and without decimal point.

| Code | Character |
| --- | --- |
| 0x00 | ![00 character]({{site.baseurl}}/assets/osd-alphabet/00.png) |
| 0x0201 | ![0201 character]({{site.baseurl}}/assets/osd-alphabet/0201.png) |
| 0x04 | ![04 character]({{site.baseurl}}/assets/osd-alphabet/04.png) |
| 0x0605 | ![0605 character]({{site.baseurl}}/assets/osd-alphabet/0605.png) |
| 0x08 | ![08 character]({{site.baseurl}}/assets/osd-alphabet/08.png) |
| 0x0A09 | ![0A09 character]({{site.baseurl}}/assets/osd-alphabet/0A09.png) |
| 0x0C | ![0C character]({{site.baseurl}}/assets/osd-alphabet/0C.png) |
| 0x0E0D | ![0E0D character]({{site.baseurl}}/assets/osd-alphabet/0E0D.png) |
| 0x0F | ![0F character]({{site.baseurl}}/assets/osd-alphabet/0F.png) |
| 0x10 | ![10 character]({{site.baseurl}}/assets/osd-alphabet/10.png) |
| 0x1211 | ![1211 character]({{site.baseurl}}/assets/osd-alphabet/1211.png) |
| 0x13 | ![13 character]({{site.baseurl}}/assets/osd-alphabet/13.png) |
| 0x14 | ![14 character]({{site.baseurl}}/assets/osd-alphabet/14.png) |
| 0x1615 | ![1615 character]({{site.baseurl}}/assets/osd-alphabet/1615.png) |
| 0x18 | ![18 character]({{site.baseurl}}/assets/osd-alphabet/18.png) |
| 0x1A19 | ![1A19 character]({{site.baseurl}}/assets/osd-alphabet/1A19.png) |
| 0x1C | ![1C character]({{site.baseurl}}/assets/osd-alphabet/1C.png) |
| 0x1E1D | ![1E1D character]({{site.baseurl}}/assets/osd-alphabet/1E1D.png) |
| 0x1F | ![1F character]({{site.baseurl}}/assets/osd-alphabet/1F.png) |
| 0x20 | ![20 character]({{site.baseurl}}/assets/osd-alphabet/20.png) |
| 0x2221 | ![2221 character]({{site.baseurl}}/assets/osd-alphabet/2221.png) |
| 0x24 | ![24 character]({{site.baseurl}}/assets/osd-alphabet/24.png) |
| 0x2625 | ![2625 character]({{site.baseurl}}/assets/osd-alphabet/2625.png) |
| 0x28 | ![28 character]({{site.baseurl}}/assets/osd-alphabet/28.png) |
| 0x2A | ![2A character]({{site.baseurl}}/assets/osd-alphabet/2A.png) |
| 0x2B | ![2B character]({{site.baseurl}}/assets/osd-alphabet/2B.png) |
| 0x2C | ![2C character]({{site.baseurl}}/assets/osd-alphabet/2C.png) |
| 0x2D | ![2D character]({{site.baseurl}}/assets/osd-alphabet/2D.png) |
| 0x2E | ![2E character]({{site.baseurl}}/assets/osd-alphabet/2E.png) |
| 0x30 | ![30 character]({{site.baseurl}}/assets/osd-alphabet/30.png) |
| 0x31 | ![31 character]({{site.baseurl}}/assets/osd-alphabet/31.png) |
| 0x32 | ![32 character]({{site.baseurl}}/assets/osd-alphabet/32.png) |
| 0x34 | ![34 character]({{site.baseurl}}/assets/osd-alphabet/34.png) |
| 0x35 | ![35 character]({{site.baseurl}}/assets/osd-alphabet/35.png) |
| 0x36 | ![36 character]({{site.baseurl}}/assets/osd-alphabet/36.png) |
| 0x3837 | ![3837 character]({{site.baseurl}}/assets/osd-alphabet/3837.png) |
| 0x3A | ![3A character]({{site.baseurl}}/assets/osd-alphabet/3A.png) |
| 0x3C3B | ![3C3B character]({{site.baseurl}}/assets/osd-alphabet/3C3B.png) |
| 0x3E | ![3E character]({{site.baseurl}}/assets/osd-alphabet/3E.png) |
| 0x403F | ![403F character]({{site.baseurl}}/assets/osd-alphabet/403F.png) |
| 0x42 | ![42 character]({{site.baseurl}}/assets/osd-alphabet/42.png) |
| 0x4443 | ![4443 character]({{site.baseurl}}/assets/osd-alphabet/4443.png) |
| 0x46 | ![46 character]({{site.baseurl}}/assets/osd-alphabet/46.png) |
| 0x4847 | ![4847 character]({{site.baseurl}}/assets/osd-alphabet/4847.png) |
| 0x4A | ![4A character]({{site.baseurl}}/assets/osd-alphabet/4A.png) |
| 0x4C4B | ![4C4B character]({{site.baseurl}}/assets/osd-alphabet/4C4B.png) |
| 0x4E | ![4E character]({{site.baseurl}}/assets/osd-alphabet/4E.png) |
| 0x504F | ![504F character]({{site.baseurl}}/assets/osd-alphabet/504F.png) |
| 0x52 | ![52 character]({{site.baseurl}}/assets/osd-alphabet/52.png) |
| 0x5453 | ![5453 character]({{site.baseurl}}/assets/osd-alphabet/5453.png) |
| 0x56 | ![56 character]({{site.baseurl}}/assets/osd-alphabet/56.png) |
| 0x5857 | ![5857 character]({{site.baseurl}}/assets/osd-alphabet/5857.png) |
| 0x5A | ![5A character]({{site.baseurl}}/assets/osd-alphabet/5A.png) |
| 0x5C5B | ![5C5B character]({{site.baseurl}}/assets/osd-alphabet/5C5B.png) |
| 0x5E | ![5E character]({{site.baseurl}}/assets/osd-alphabet/5E.png) |
| 0x60 | ![60 character]({{site.baseurl}}/assets/osd-alphabet/60.png) |
| 0x62 | ![62 character]({{site.baseurl}}/assets/osd-alphabet/62.png) |
| 0x64 | ![64 character]({{site.baseurl}}/assets/osd-alphabet/64.png) |
| 0x66 | ![66 character]({{site.baseurl}}/assets/osd-alphabet/66.png) |
| 0x68 | ![68 character]({{site.baseurl}}/assets/osd-alphabet/68.png) |
| 0x6A | ![6A character]({{site.baseurl}}/assets/osd-alphabet/6A.png) |
| 0x6C | ![6C character]({{site.baseurl}}/assets/osd-alphabet/6C.png) |
| 0x6E6D | ![6E6D character]({{site.baseurl}}/assets/osd-alphabet/6E6D.png) |
| 0x70 | ![70 character]({{site.baseurl}}/assets/osd-alphabet/70.png) |
| 0x7271 | ![7271 character]({{site.baseurl}}/assets/osd-alphabet/7271.png) |
| 0x74 | ![74 character]({{site.baseurl}}/assets/osd-alphabet/74.png) |
| 0x7675 | ![7675 character]({{site.baseurl}}/assets/osd-alphabet/7675.png) |
| 0x78 | ![78 character]({{site.baseurl}}/assets/osd-alphabet/78.png) |
| 0x7B7A | ![7B7A character]({{site.baseurl}}/assets/osd-alphabet/7B7A.png) |
| 0x7C | ![7C character]({{site.baseurl}}/assets/osd-alphabet/7C.png) |
| 0x7D | ![7D character]({{site.baseurl}}/assets/osd-alphabet/7D.png) |
| 0x7E | ![7E character]({{site.baseurl}}/assets/osd-alphabet/7E.png) |
| 0x8180 | ![8180 character]({{site.baseurl}}/assets/osd-alphabet/8180.png) |
| 0x82 | ![82 character]({{site.baseurl}}/assets/osd-alphabet/82.png) |
| 0x84 | ![84 character]({{site.baseurl}}/assets/osd-alphabet/84.png) |
| 0x86 | ![86 character]({{site.baseurl}}/assets/osd-alphabet/86.png) |
| 0x88 | ![88 character]({{site.baseurl}}/assets/osd-alphabet/88.png) |
| 0x8A | ![8A character]({{site.baseurl}}/assets/osd-alphabet/8A.png) |
| 0x8C | ![8C character]({{site.baseurl}}/assets/osd-alphabet/8C.png) |
| 0x8E | ![8E character]({{site.baseurl}}/assets/osd-alphabet/8E.png) |
| 0x90 | ![90 character]({{site.baseurl}}/assets/osd-alphabet/90.png) |
| 0x92 | ![92 character]({{site.baseurl}}/assets/osd-alphabet/92.png) |
| 0x94 | ![94 character]({{site.baseurl}}/assets/osd-alphabet/94.png) |
| 0x96 | ![96 character]({{site.baseurl}}/assets/osd-alphabet/96.png) |
| 0x98 | ![98 character]({{site.baseurl}}/assets/osd-alphabet/98.png) |
| 0x9A99 | ![9A99 character]({{site.baseurl}}/assets/osd-alphabet/9A99.png) |
| 0x9C | ![9C character]({{site.baseurl}}/assets/osd-alphabet/9C.png) |
| 0x9E9D | ![9E9D character]({{site.baseurl}}/assets/osd-alphabet/9E9D.png) |
| 0xA0 | ![A0 character]({{site.baseurl}}/assets/osd-alphabet/A0.png) |
| 0xA2A1 | ![A2A1 character]({{site.baseurl}}/assets/osd-alphabet/A2A1.png) |
| 0xA5A4 | ![A5A4 character]({{site.baseurl}}/assets/osd-alphabet/A5A4.png) |
| 0xA6 | ![A6 character]({{site.baseurl}}/assets/osd-alphabet/A6.png) |
| 0xA7 | ![A7 character]({{site.baseurl}}/assets/osd-alphabet/A7.png) |
| 0xA8 | ![A8 character]({{site.baseurl}}/assets/osd-alphabet/A8.png) |
| 0xA9 | ![A9 character]({{site.baseurl}}/assets/osd-alphabet/A9.png) |
| 0xAA | ![AA character]({{site.baseurl}}/assets/osd-alphabet/AA.png) |
| 0xAC | ![AC character]({{site.baseurl}}/assets/osd-alphabet/AC.png) |
| 0xAEAD | ![AEAD character]({{site.baseurl}}/assets/osd-alphabet/AEAD.png) |
| 0xB0 | ![B0 character]({{site.baseurl}}/assets/osd-alphabet/B0.png) |
| 0xB1 | ![B1 character]({{site.baseurl}}/assets/osd-alphabet/B1.png) |
| 0xB2 | ![B2 character]({{site.baseurl}}/assets/osd-alphabet/B2.png) |
| 0xB4 | ![B4 character]({{site.baseurl}}/assets/osd-alphabet/B4.png) |
| 0xB5 | ![B5 character]({{site.baseurl}}/assets/osd-alphabet/B5.png) |
| 0xB6 | ![B6 character]({{site.baseurl}}/assets/osd-alphabet/B6.png) |
| 0xB8 | ![B8 character]({{site.baseurl}}/assets/osd-alphabet/B8.png) |
| 0xBAB9 | ![BAB9 character]({{site.baseurl}}/assets/osd-alphabet/BAB9.png) |
| 0xBC | ![BC character]({{site.baseurl}}/assets/osd-alphabet/BC.png) |
| 0xBD | ![BD character]({{site.baseurl}}/assets/osd-alphabet/BD.png) |
| 0xBE | ![BE character]({{site.baseurl}}/assets/osd-alphabet/BE.png) |
| 0xC0 | ![C0 character]({{site.baseurl}}/assets/osd-alphabet/C0.png) |
| 0xC2C1 | ![C2C1 character]({{site.baseurl}}/assets/osd-alphabet/C2C1.png) |
| 0xC4 | ![C4 character]({{site.baseurl}}/assets/osd-alphabet/C4.png) |
| 0xC6C5 | ![C6C5 character]({{site.baseurl}}/assets/osd-alphabet/C6C5.png) |
| 0xC8 | ![C8 character]({{site.baseurl}}/assets/osd-alphabet/C8.png) |
| 0xCAC9 | ![CAC9 character]({{site.baseurl}}/assets/osd-alphabet/CAC9.png) |
| 0xCC | ![CC character]({{site.baseurl}}/assets/osd-alphabet/CC.png) |
| 0xCECD | ![CECD character]({{site.baseurl}}/assets/osd-alphabet/CECD.png) |
| 0xD0 | ![D0 character]({{site.baseurl}}/assets/osd-alphabet/D0.png) |
| 0xD2D1 | ![D2D1 character]({{site.baseurl}}/assets/osd-alphabet/D2D1.png) |
| 0xD4 | ![D4 character]({{site.baseurl}}/assets/osd-alphabet/D4.png) |
| 0xD6D5 | ![D6D5 character]({{site.baseurl}}/assets/osd-alphabet/D6D5.png) |
| 0xD8D7 | ![D8D7 character]({{site.baseurl}}/assets/osd-alphabet/D8D7.png) |
| 0xDAD9 | ![DAD9 character]({{site.baseurl}}/assets/osd-alphabet/DAD9.png) |
| 0xDCDB | ![DCDB character]({{site.baseurl}}/assets/osd-alphabet/DCDB.png) |
| 0xDFDE | ![DFDE character]({{site.baseurl}}/assets/osd-alphabet/DFDE.png) |
| 0xE0 | ![E0 character]({{site.baseurl}}/assets/osd-alphabet/E0.png) |
| 0xE2E1 | ![E2E1 character]({{site.baseurl}}/assets/osd-alphabet/E2E1.png) |
| 0xE4 | ![E4 character]({{site.baseurl}}/assets/osd-alphabet/E4.png) |
| 0xE6 | ![E6 character]({{site.baseurl}}/assets/osd-alphabet/E6.png) |
| 0xE8 | ![E8 character]({{site.baseurl}}/assets/osd-alphabet/E8.png) |
| 0xEA | ![EA character]({{site.baseurl}}/assets/osd-alphabet/EA.png) |
| 0xEC | ![EC character]({{site.baseurl}}/assets/osd-alphabet/EC.png) |
| 0xED | ![ED character]({{site.baseurl}}/assets/osd-alphabet/ED.png) |
| 0xEE | ![EE character]({{site.baseurl}}/assets/osd-alphabet/EE.png) |
| 0xF0 | ![F0 character]({{site.baseurl}}/assets/osd-alphabet/F0.png) |
| 0xF1 | ![F1 character]({{site.baseurl}}/assets/osd-alphabet/F1.png) |
| 0xF3F2 | ![F3F2 character]({{site.baseurl}}/assets/osd-alphabet/F3F2.png) |
| 0xF5F4 | ![F5F4 character]({{site.baseurl}}/assets/osd-alphabet/F5F4.png) |
| 0xF6 | ![F6 character]({{site.baseurl}}/assets/osd-alphabet/F6.png) |
| 0xF7 | ![F7 character]({{site.baseurl}}/assets/osd-alphabet/F7.png) |
| 0xF8 | ![F8 character]({{site.baseurl}}/assets/osd-alphabet/F8.png) |
| 0xFA | ![FA character]({{site.baseurl}}/assets/osd-alphabet/FA.png) |
| 0xFB | ![FB character]({{site.baseurl}}/assets/osd-alphabet/FB.png) |
| 0xFC | ![FC character]({{site.baseurl}}/assets/osd-alphabet/FC.png) |
| 0xFE | ![FE character]({{site.baseurl}}/assets/osd-alphabet/FE.png) |
