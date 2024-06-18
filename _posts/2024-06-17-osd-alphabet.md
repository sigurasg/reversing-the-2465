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
The first subsumed code is 0x07, followed by 0x0B.

It's also interesting to note that most of the digits occur 4 times in this alphabet, as they
appear large and small, as well as with and without decimal point.

Why is it that some of the large ones don't appear with a decimal point?

| Code | Character |
| --- | --- |
| 0x00 | ![0x00 character]({{site.baseurl}}/assets/osd-alphabet/00.png) |
| 0x0201 | ![0x0201 character]({{site.baseurl}}/assets/osd-alphabet/0201.png) |
| 0x04 | ![0x04 character]({{site.baseurl}}/assets/osd-alphabet/04.png) |
| 0x0605 | ![0x0605 character]({{site.baseurl}}/assets/osd-alphabet/0605.png) |
| 0x08 | ![0x08 character]({{site.baseurl}}/assets/osd-alphabet/08.png) |
| 0x0A09 | ![0x0A09 character]({{site.baseurl}}/assets/osd-alphabet/0A09.png) |
| 0x0C | ![0x0C character]({{site.baseurl}}/assets/osd-alphabet/0C.png) |
| 0x0E0D | ![0x0E0D character]({{site.baseurl}}/assets/osd-alphabet/0E0D.png) |
| 0x10 | ![0x10 character]({{site.baseurl}}/assets/osd-alphabet/10.png) |
| 0x1211 | ![0x1211 character]({{site.baseurl}}/assets/osd-alphabet/1211.png) |
| 0x1615 | ![0x1615 character]({{site.baseurl}}/assets/osd-alphabet/1615.png) |
| 0x18 | ![0x18 character]({{site.baseurl}}/assets/osd-alphabet/18.png) |
| 0x1C | ![0x1C character]({{site.baseurl}}/assets/osd-alphabet/1C.png) |
| 0x1E1D | ![0x1E1D character]({{site.baseurl}}/assets/osd-alphabet/1E1D.png) |
| 0x2221 | ![0x2221 character]({{site.baseurl}}/assets/osd-alphabet/2221.png) |
| 0x24 | ![0x24 character]({{site.baseurl}}/assets/osd-alphabet/24.png) |
| 0x2625 | ![0x2625 character]({{site.baseurl}}/assets/osd-alphabet/2625.png) |
| 0x28 | ![0x28 character]({{site.baseurl}}/assets/osd-alphabet/28.png) |
| 0x29 | ![0x29 character]({{site.baseurl}}/assets/osd-alphabet/29.png) |
| 0x2A | ![0x2A character]({{site.baseurl}}/assets/osd-alphabet/2A.png) |
| 0x2B | ![0x2B character]({{site.baseurl}}/assets/osd-alphabet/2B.png) |
| 0x2C | ![0x2C character]({{site.baseurl}}/assets/osd-alphabet/2C.png) |
| 0x2D | ![0x2D character]({{site.baseurl}}/assets/osd-alphabet/2D.png) |
| 0x2E | ![0x2E character]({{site.baseurl}}/assets/osd-alphabet/2E.png) |
| 0x2F | ![0x2F character]({{site.baseurl}}/assets/osd-alphabet/2F.png) |
| 0x30 | ![0x30 character]({{site.baseurl}}/assets/osd-alphabet/30.png) |
| 0x31 | ![0x31 character]({{site.baseurl}}/assets/osd-alphabet/31.png) |
| 0x32 | ![0x32 character]({{site.baseurl}}/assets/osd-alphabet/32.png) |
| 0x33 | ![0x33 character]({{site.baseurl}}/assets/osd-alphabet/33.png) |
| 0x34 | ![0x34 character]({{site.baseurl}}/assets/osd-alphabet/34.png) |
| 0x35 | ![0x35 character]({{site.baseurl}}/assets/osd-alphabet/35.png) |
| 0x3A | ![0x3A character]({{site.baseurl}}/assets/osd-alphabet/3A.png) |
| 0x3E | ![0x3E character]({{site.baseurl}}/assets/osd-alphabet/3E.png) |
| 0x42 | ![0x42 character]({{site.baseurl}}/assets/osd-alphabet/42.png) |
| 0x4443 | ![0x4443 character]({{site.baseurl}}/assets/osd-alphabet/4443.png) |
| 0x46 | ![0x46 character]({{site.baseurl}}/assets/osd-alphabet/46.png) |
| 0x4847 | ![0x4847 character]({{site.baseurl}}/assets/osd-alphabet/4847.png) |
| 0x4A | ![0x4A character]({{site.baseurl}}/assets/osd-alphabet/4A.png) |
| 0x4C4B | ![0x4C4B character]({{site.baseurl}}/assets/osd-alphabet/4C4B.png) |
| 0x4E | ![0x4E character]({{site.baseurl}}/assets/osd-alphabet/4E.png) |
| 0x52 | ![0x52 character]({{site.baseurl}}/assets/osd-alphabet/52.png) |
| 0x5453 | ![0x5453 character]({{site.baseurl}}/assets/osd-alphabet/5453.png) |
| 0x56 | ![0x56 character]({{site.baseurl}}/assets/osd-alphabet/56.png) |
| 0x5857 | ![0x5857 character]({{site.baseurl}}/assets/osd-alphabet/5857.png) |
| 0x5A | ![0x5A character]({{site.baseurl}}/assets/osd-alphabet/5A.png) |
| 0x5C5B | ![0x5C5B character]({{site.baseurl}}/assets/osd-alphabet/5C5B.png) |
| 0x5E | ![0x5E character]({{site.baseurl}}/assets/osd-alphabet/5E.png) |
| 0x60 | ![0x60 character]({{site.baseurl}}/assets/osd-alphabet/60.png) |
| 0x62 | ![0x62 character]({{site.baseurl}}/assets/osd-alphabet/62.png) |
| 0x64 | ![0x64 character]({{site.baseurl}}/assets/osd-alphabet/64.png) |
| 0x66 | ![0x66 character]({{site.baseurl}}/assets/osd-alphabet/66.png) |
| 0x68 | ![0x68 character]({{site.baseurl}}/assets/osd-alphabet/68.png) |
| 0x6A | ![0x6A character]({{site.baseurl}}/assets/osd-alphabet/6A.png) |
| 0x6C | ![0x6C character]({{site.baseurl}}/assets/osd-alphabet/6C.png) |
| 0x6E6D | ![0x6E6D character]({{site.baseurl}}/assets/osd-alphabet/6E6D.png) |
| 0x70 | ![0x70 character]({{site.baseurl}}/assets/osd-alphabet/70.png) |
| 0x7271 | ![0x7271 character]({{site.baseurl}}/assets/osd-alphabet/7271.png) |
| 0x74 | ![0x74 character]({{site.baseurl}}/assets/osd-alphabet/74.png) |
| 0x7675 | ![0x7675 character]({{site.baseurl}}/assets/osd-alphabet/7675.png) |
| 0x78 | ![0x78 character]({{site.baseurl}}/assets/osd-alphabet/78.png) |
| 0x7B7A | ![0x7B7A character]({{site.baseurl}}/assets/osd-alphabet/7B7A.png) |
| 0x7D | ![0x7D character]({{site.baseurl}}/assets/osd-alphabet/7D.png) |
| 0x7E | ![0x7E character]({{site.baseurl}}/assets/osd-alphabet/7E.png) |
| 0x8180 | ![0x8180 character]({{site.baseurl}}/assets/osd-alphabet/8180.png) |
| 0x84 | ![0x84 character]({{site.baseurl}}/assets/osd-alphabet/84.png) |
| 0x86 | ![0x86 character]({{site.baseurl}}/assets/osd-alphabet/86.png) |
| 0x88 | ![0x88 character]({{site.baseurl}}/assets/osd-alphabet/88.png) |
| 0x89 | ![0x89 character]({{site.baseurl}}/assets/osd-alphabet/89.png) |
| 0x8A | ![0x8A character]({{site.baseurl}}/assets/osd-alphabet/8A.png) |
| 0x8B | ![0x8B character]({{site.baseurl}}/assets/osd-alphabet/8B.png) |
| 0x8C | ![0x8C character]({{site.baseurl}}/assets/osd-alphabet/8C.png) |
| 0x8D | ![0x8D character]({{site.baseurl}}/assets/osd-alphabet/8D.png) |
| 0x8E | ![0x8E character]({{site.baseurl}}/assets/osd-alphabet/8E.png) |
| 0x8F | ![0x8F character]({{site.baseurl}}/assets/osd-alphabet/8F.png) |
| 0x90 | ![0x90 character]({{site.baseurl}}/assets/osd-alphabet/90.png) |
| 0x91 | ![0x91 character]({{site.baseurl}}/assets/osd-alphabet/91.png) |
| 0x92 | ![0x92 character]({{site.baseurl}}/assets/osd-alphabet/92.png) |
| 0x93 | ![0x93 character]({{site.baseurl}}/assets/osd-alphabet/93.png) |
| 0x94 | ![0x94 character]({{site.baseurl}}/assets/osd-alphabet/94.png) |
| 0x95 | ![0x95 character]({{site.baseurl}}/assets/osd-alphabet/95.png) |
| 0x96 | ![0x96 character]({{site.baseurl}}/assets/osd-alphabet/96.png) |
| 0x97 | ![0x97 character]({{site.baseurl}}/assets/osd-alphabet/97.png) |
| 0x98 | ![0x98 character]({{site.baseurl}}/assets/osd-alphabet/98.png) |
| 0x99 | ![0x99 character]({{site.baseurl}}/assets/osd-alphabet/99.png) |
| 0x9A | ![0x9A character]({{site.baseurl}}/assets/osd-alphabet/9A.png) |
| 0x9C | ![0x9C character]({{site.baseurl}}/assets/osd-alphabet/9C.png) |
| 0x9E9D | ![0x9E9D character]({{site.baseurl}}/assets/osd-alphabet/9E9D.png) |
| 0xA0 | ![0xA0 character]({{site.baseurl}}/assets/osd-alphabet/A0.png) |
| 0xA2A1 | ![0xA2A1 character]({{site.baseurl}}/assets/osd-alphabet/A2A1.png) |
| 0xA4 | ![0xA4 character]({{site.baseurl}}/assets/osd-alphabet/A4.png) |
| 0xA7 | ![0xA7 character]({{site.baseurl}}/assets/osd-alphabet/A7.png) |
| 0xA8 | ![0xA8 character]({{site.baseurl}}/assets/osd-alphabet/A8.png) |
| 0xA9 | ![0xA9 character]({{site.baseurl}}/assets/osd-alphabet/A9.png) |
| 0xAA | ![0xAA character]({{site.baseurl}}/assets/osd-alphabet/AA.png) |
| 0xAC | ![0xAC character]({{site.baseurl}}/assets/osd-alphabet/AC.png) |
| 0xAEAD | ![0xAEAD character]({{site.baseurl}}/assets/osd-alphabet/AEAD.png) |
| 0xB0 | ![0xB0 character]({{site.baseurl}}/assets/osd-alphabet/B0.png) |
| 0xB1 | ![0xB1 character]({{site.baseurl}}/assets/osd-alphabet/B1.png) |
| 0xB2 | ![0xB2 character]({{site.baseurl}}/assets/osd-alphabet/B2.png) |
| 0xB4 | ![0xB4 character]({{site.baseurl}}/assets/osd-alphabet/B4.png) |
| 0xB5 | ![0xB5 character]({{site.baseurl}}/assets/osd-alphabet/B5.png) |
| 0xB6 | ![0xB6 character]({{site.baseurl}}/assets/osd-alphabet/B6.png) |
| 0xB8 | ![0xB8 character]({{site.baseurl}}/assets/osd-alphabet/B8.png) |
| 0xBAB9 | ![0xBAB9 character]({{site.baseurl}}/assets/osd-alphabet/BAB9.png) |
| 0xBC | ![0xBC character]({{site.baseurl}}/assets/osd-alphabet/BC.png) |
| 0xBD | ![0xBD character]({{site.baseurl}}/assets/osd-alphabet/BD.png) |
| 0xBE | ![0xBE character]({{site.baseurl}}/assets/osd-alphabet/BE.png) |
| 0xC0 | ![0xC0 character]({{site.baseurl}}/assets/osd-alphabet/C0.png) |
| 0xC4 | ![0xC4 character]({{site.baseurl}}/assets/osd-alphabet/C4.png) |
| 0xC6C5 | ![0xC6C5 character]({{site.baseurl}}/assets/osd-alphabet/C6C5.png) |
| 0xC8 | ![0xC8 character]({{site.baseurl}}/assets/osd-alphabet/C8.png) |
| 0xCAC9 | ![0xCAC9 character]({{site.baseurl}}/assets/osd-alphabet/CAC9.png) |
| 0xCC | ![0xCC character]({{site.baseurl}}/assets/osd-alphabet/CC.png) |
| 0xCECD | ![0xCECD character]({{site.baseurl}}/assets/osd-alphabet/CECD.png) |
| 0xD0 | ![0xD0 character]({{site.baseurl}}/assets/osd-alphabet/D0.png) |
| 0xD2D1 | ![0xD2D1 character]({{site.baseurl}}/assets/osd-alphabet/D2D1.png) |
| 0xD4 | ![0xD4 character]({{site.baseurl}}/assets/osd-alphabet/D4.png) |
| 0xD6D5 | ![0xD6D5 character]({{site.baseurl}}/assets/osd-alphabet/D6D5.png) |
| 0xDE | ![0xDE character]({{site.baseurl}}/assets/osd-alphabet/DE.png) |
| 0xE3 | ![0xE3 character]({{site.baseurl}}/assets/osd-alphabet/E3.png) |
| 0xE4 | ![0xE4 character]({{site.baseurl}}/assets/osd-alphabet/E4.png) |
| 0xE8 | ![0xE8 character]({{site.baseurl}}/assets/osd-alphabet/E8.png) |
| 0xEA | ![0xEA character]({{site.baseurl}}/assets/osd-alphabet/EA.png) |
| 0xEC | ![0xEC character]({{site.baseurl}}/assets/osd-alphabet/EC.png) |
| 0xEE | ![0xEE character]({{site.baseurl}}/assets/osd-alphabet/EE.png) |
| 0xF0 | ![0xF0 character]({{site.baseurl}}/assets/osd-alphabet/F0.png) |
| 0xF8 | ![0xF8 character]({{site.baseurl}}/assets/osd-alphabet/F8.png) |
| 0xFA | ![0xFA character]({{site.baseurl}}/assets/osd-alphabet/FA.png) |
| 0xFB | ![0xFB character]({{site.baseurl}}/assets/osd-alphabet/FB.png) |
| 0xFE | ![0xFE character]({{site.baseurl}}/assets/osd-alphabet/FE.png) |
| 0xFF | ![0xFF character]({{site.baseurl}}/assets/osd-alphabet/FF.png) |
