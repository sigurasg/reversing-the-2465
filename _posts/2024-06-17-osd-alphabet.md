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
Thankfully the Theory of Operation (ThO) section in the service manuals has a solid explanation of how
the readout system renders the contents of character RAM to the CRT, through the contents of the
character ROM.
The ThO describes how the circuitry works in excruciating detail, but the gist of it is this:
1. A byte in the readout RAM selects a character to display.
   The start address of the character in character ROM is simply character value * 16.
2. Readout characters comprise a series of dots, where each is encoded into a byte.
   The X, Y coordinates of each dot are encoded in 3 and 4 bits of the byte, respectively,
   so each character can therefore be as wide as 8 dots, but as tall as 16.
3. A dot with the MSB clear ends the character.

The way this is laid out is that some characters are designed to be used in pairs to make
a single large character.
As the end goal is to be able to convert a series of OSD bytes into a human-readable string,
these pairwise characters are considered a single character for our purposes.

After some experimentation, I wrote a relatively simple python script to convert the character
ROM to a bunch of PNG files.

# The alphabet

| Code | Character |
| --- | --- |
| 00 | ![00 character](/asserts/osd-alphabet/00.png) |
| 0201 | ![0201 character](/asserts/osd-alphabet/0201.png) |
| 04 | ![04 character](/asserts/osd-alphabet/04.png) |
| 0605 | ![0605 character](/asserts/osd-alphabet/0605.png) |
| 08 | ![08 character](/asserts/osd-alphabet/08.png) |
| 0A09 | ![0A09 character](/asserts/osd-alphabet/0A09.png) |
| 0C | ![0C character](/asserts/osd-alphabet/0C.png) |
| 0E0D | ![0E0D character](/asserts/osd-alphabet/0E0D.png) |
| 10 | ![10 character](/asserts/osd-alphabet/10.png) |
| 1211 | ![1211 character](/asserts/osd-alphabet/1211.png) |
| 1615 | ![1615 character](/asserts/osd-alphabet/1615.png) |
| 18 | ![18 character](/asserts/osd-alphabet/18.png) |
| 1C | ![1C character](/asserts/osd-alphabet/1C.png) |
| 1E1D | ![1E1D character](/asserts/osd-alphabet/1E1D.png) |
| 2221 | ![2221 character](/asserts/osd-alphabet/2221.png) |
| 24 | ![24 character](/asserts/osd-alphabet/24.png) |
| 2625 | ![2625 character](/asserts/osd-alphabet/2625.png) |
| 28 | ![28 character](/asserts/osd-alphabet/28.png) |
| 29 | ![29 character](/asserts/osd-alphabet/29.png) |
| 2A | ![2A character](/asserts/osd-alphabet/2A.png) |
| 2B | ![2B character](/asserts/osd-alphabet/2B.png) |
| 2C | ![2C character](/asserts/osd-alphabet/2C.png) |
| 2D | ![2D character](/asserts/osd-alphabet/2D.png) |
| 2E | ![2E character](/asserts/osd-alphabet/2E.png) |
| 2F | ![2F character](/asserts/osd-alphabet/2F.png) |
| 30 | ![30 character](/asserts/osd-alphabet/30.png) |
| 31 | ![31 character](/asserts/osd-alphabet/31.png) |
| 32 | ![32 character](/asserts/osd-alphabet/32.png) |
| 33 | ![33 character](/asserts/osd-alphabet/33.png) |
| 34 | ![34 character](/asserts/osd-alphabet/34.png) |
| 35 | ![35 character](/asserts/osd-alphabet/35.png) |
| 3A | ![3A character](/asserts/osd-alphabet/3A.png) |
| 3E | ![3E character](/asserts/osd-alphabet/3E.png) |
| 42 | ![42 character](/asserts/osd-alphabet/42.png) |
| 4443 | ![4443 character](/asserts/osd-alphabet/4443.png) |
| 46 | ![46 character](/asserts/osd-alphabet/46.png) |
| 4847 | ![4847 character](/asserts/osd-alphabet/4847.png) |
| 4A | ![4A character](/asserts/osd-alphabet/4A.png) |
| 4C4B | ![4C4B character](/asserts/osd-alphabet/4C4B.png) |
| 4E | ![4E character](/asserts/osd-alphabet/4E.png) |
| 52 | ![52 character](/asserts/osd-alphabet/52.png) |
| 5453 | ![5453 character](/asserts/osd-alphabet/5453.png) |
| 56 | ![56 character](/asserts/osd-alphabet/56.png) |
| 5857 | ![5857 character](/asserts/osd-alphabet/5857.png) |
| 5A | ![5A character](/asserts/osd-alphabet/5A.png) |
| 5C5B | ![5C5B character](/asserts/osd-alphabet/5C5B.png) |
| 5E | ![5E character](/asserts/osd-alphabet/5E.png) |
| 60 | ![60 character](/asserts/osd-alphabet/60.png) |
| 62 | ![62 character](/asserts/osd-alphabet/62.png) |
| 64 | ![64 character](/asserts/osd-alphabet/64.png) |
| 66 | ![66 character](/asserts/osd-alphabet/66.png) |
| 68 | ![68 character](/asserts/osd-alphabet/68.png) |
| 6A | ![6A character](/asserts/osd-alphabet/6A.png) |
| 6C | ![6C character](/asserts/osd-alphabet/6C.png) |
| 6E6D | ![6E6D character](/asserts/osd-alphabet/6E6D.png) |
| 70 | ![70 character](/asserts/osd-alphabet/70.png) |
| 7271 | ![7271 character](/asserts/osd-alphabet/7271.png) |
| 74 | ![74 character](/asserts/osd-alphabet/74.png) |
| 7675 | ![7675 character](/asserts/osd-alphabet/7675.png) |
| 78 | ![78 character](/asserts/osd-alphabet/78.png) |
| 7B7A | ![7B7A character](/asserts/osd-alphabet/7B7A.png) |
| 7D | ![7D character](/asserts/osd-alphabet/7D.png) |
| 7E | ![7E character](/asserts/osd-alphabet/7E.png) |
| 8180 | ![8180 character](/asserts/osd-alphabet/8180.png) |
| 84 | ![84 character](/asserts/osd-alphabet/84.png) |
| 86 | ![86 character](/asserts/osd-alphabet/86.png) |
| 88 | ![88 character](/asserts/osd-alphabet/88.png) |
| 89 | ![89 character](/asserts/osd-alphabet/89.png) |
| 8A | ![8A character](/asserts/osd-alphabet/8A.png) |
| 8B | ![8B character](/asserts/osd-alphabet/8B.png) |
| 8C | ![8C character](/asserts/osd-alphabet/8C.png) |
| 8D | ![8D character](/asserts/osd-alphabet/8D.png) |
| 8E | ![8E character](/asserts/osd-alphabet/8E.png) |
| 8F | ![8F character](/asserts/osd-alphabet/8F.png) |
| 90 | ![90 character](/asserts/osd-alphabet/90.png) |
| 91 | ![91 character](/asserts/osd-alphabet/91.png) |
| 92 | ![92 character](/asserts/osd-alphabet/92.png) |
| 93 | ![93 character](/asserts/osd-alphabet/93.png) |
| 94 | ![94 character](/asserts/osd-alphabet/94.png) |
| 95 | ![95 character](/asserts/osd-alphabet/95.png) |
| 96 | ![96 character](/asserts/osd-alphabet/96.png) |
| 97 | ![97 character](/asserts/osd-alphabet/97.png) |
| 98 | ![98 character](/asserts/osd-alphabet/98.png) |
| 99 | ![99 character](/asserts/osd-alphabet/99.png) |
| 9A | ![9A character](/asserts/osd-alphabet/9A.png) |
| 9C | ![9C character](/asserts/osd-alphabet/9C.png) |
| 9E9D | ![9E9D character](/asserts/osd-alphabet/9E9D.png) |
| A0 | ![A0 character](/asserts/osd-alphabet/A0.png) |
| A2A1 | ![A2A1 character](/asserts/osd-alphabet/A2A1.png) |
| A4 | ![A4 character](/asserts/osd-alphabet/A4.png) |
| A7 | ![A7 character](/asserts/osd-alphabet/A7.png) |
| A8 | ![A8 character](/asserts/osd-alphabet/A8.png) |
| A9 | ![A9 character](/asserts/osd-alphabet/A9.png) |
| AA | ![AA character](/asserts/osd-alphabet/AA.png) |
| AC | ![AC character](/asserts/osd-alphabet/AC.png) |
| AEAD | ![AEAD character](/asserts/osd-alphabet/AEAD.png) |
| B0 | ![B0 character](/asserts/osd-alphabet/B0.png) |
| B1 | ![B1 character](/asserts/osd-alphabet/B1.png) |
| B2 | ![B2 character](/asserts/osd-alphabet/B2.png) |
| B4 | ![B4 character](/asserts/osd-alphabet/B4.png) |
| B5 | ![B5 character](/asserts/osd-alphabet/B5.png) |
| B6 | ![B6 character](/asserts/osd-alphabet/B6.png) |
| B8 | ![B8 character](/asserts/osd-alphabet/B8.png) |
| BAB9 | ![BAB9 character](/asserts/osd-alphabet/BAB9.png) |
| BC | ![BC character](/asserts/osd-alphabet/BC.png) |
| BD | ![BD character](/asserts/osd-alphabet/BD.png) |
| BE | ![BE character](/asserts/osd-alphabet/BE.png) |
| C0 | ![C0 character](/asserts/osd-alphabet/C0.png) |
| C4 | ![C4 character](/asserts/osd-alphabet/C4.png) |
| C6C5 | ![C6C5 character](/asserts/osd-alphabet/C6C5.png) |
| C8 | ![C8 character](/asserts/osd-alphabet/C8.png) |
| CAC9 | ![CAC9 character](/asserts/osd-alphabet/CAC9.png) |
| CC | ![CC character](/asserts/osd-alphabet/CC.png) |
| CECD | ![CECD character](/asserts/osd-alphabet/CECD.png) |
| D0 | ![D0 character](/asserts/osd-alphabet/D0.png) |
| D2D1 | ![D2D1 character](/asserts/osd-alphabet/D2D1.png) |
| D4 | ![D4 character](/asserts/osd-alphabet/D4.png) |
| D6D5 | ![D6D5 character](/asserts/osd-alphabet/D6D5.png) |
| DE | ![DE character](/asserts/osd-alphabet/DE.png) |
| E3 | ![E3 character](/asserts/osd-alphabet/E3.png) |
| E4 | ![E4 character](/asserts/osd-alphabet/E4.png) |
| E8 | ![E8 character](/asserts/osd-alphabet/E8.png) |
| EA | ![EA character](/asserts/osd-alphabet/EA.png) |
| EC | ![EC character](/asserts/osd-alphabet/EC.png) |
| EE | ![EE character](/asserts/osd-alphabet/EE.png) |
| F0 | ![F0 character](/asserts/osd-alphabet/F0.png) |
| F8 | ![F8 character](/asserts/osd-alphabet/F8.png) |
| FA | ![FA character](/asserts/osd-alphabet/FA.png) |
| FB | ![FB character](/asserts/osd-alphabet/FB.png) |
| FE | ![FE character](/asserts/osd-alphabet/FE.png) |
| FF | ![FF character](/asserts/osd-alphabet/FF.png) |

