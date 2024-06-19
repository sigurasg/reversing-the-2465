---
title: "MC6800 limitations"
date: 2024-06-18
---

# MC6800 ISA

When I first started reading the disassembled code from the 2465, I was surprised at
how it was structured.
I grew up on the [Z80](https://en.wikipedia.org/wiki/Zilog_Z80), which, while only a
couple of years "younger" than the [MC6800](https://en.wikipedia.org/wiki/Motorola_6800),
has a lot of additional instruction set features that I didn't know to miss
until I started reading MC6800 code.

# MC6800 registers

The MC6800 has a fairly small set of registers:

| Name | Width | Purpose |
| --- | --- | --- |
| A | 8 bits | Accumulator |
| B | 8 bits | Accumulator |
| X | 16 bits | Index register |
| PC | 16 bits | Program counter |
| SP | 16 bits | Stack pointer |
| SR | 8 bits | Status register |

However, as microcontrollers go this doesn't actually look too bad.

The stack pointer is a full 16 bits, so the stack can be anywhere in memory and can use any amount of available memory. This is as opposed to e.g. the
[6502](https://en.wikipedia.org/wiki/MOS_Technology_6502), which has an 8 bit stack
pointer and fixes the stack from 0x0100-0x01FF.

There are two 8 bit accumulators which makes it easy to e.g. do arithmetic on two
8 bit values.

The addressing modes are for the most part reasonable.
*  The `A`, `B` and `X` registers can be loaded from and stored to 
   arbitrary immediate addresses (whether 8 or 16 bit).
* `A` and `B` can be loaded from the other.
* `X` can be loaded from `SP` and vice versa.
* `X` can be used with offsets to e.g. index into structure fields.

# No reentrant programming

What's missing in the MC6800 instruction set, is any practical way to implement reentrant
programming.
The problem isn't with the register set, but rather with the instruction set and
addressing modes.
There's simply no practical way to store the `X` to the stack, nor to load it from
the stack.

If only there were 
* `PSH X` 
* `PUL X`

instructions, or alternatively some way to load and store `X` from `A` and `B`,
everything would work out fine.

Also, if it were possible to indirect `SP` with offset, e.g.
* `LDA A, 0x4,S`
* `STA A, 0x4,S`

it would be at least possible to pass arguments and store locals on stack.
Alas, index addressing mode is exclusive to the `X` register.

All told, what this means is that there's no practical way to pass arguments 
and store local variables on the stack, which leads to code that largely has to:
* pass arguments in globals
* store temporaries in globals
* return results in globals

e.g. non-reentrant code.

This is not to say that registers can't be used for those purposes, but what can
be done with registers is quite limited, especially `X`.

All of this leads to this sort of code:

```
        word ADD_X_B(word x, byte b)
            word              X:2            <RETURN>
            word              X:2            x
            byte              B:1            b
                ADD_X_B
        TPA                 ; Save SR in A
        SEI                 ; Disable interrupts
        STX        TMP_X    ; Store X in a temporary
        ADDB       TMP_X+1  ; Add B to LSB of temporary.
        STAB       TMP_X+1  ; Store LSB sum to the temporary.
        BCC        NO_CARRY
        INC        TMP_X    ; Increment MSB of temporary
                            ; on carry.
    NO_CARRY
        LDX        TMP_X    ; Load sum.
        TAP                 ; Restore SR (possibly
                            ; re-enabling interrupts)
        RTS
```

Since this is a general utility function and the code is non-reentrant,
it has to protect against reentrancy by disabling interrupts.

# It's not all bad

For the purposes of reverse engineering this isn't all that bad.
Since globals are used so widely, once a global that passes an argument
or returns a result has been discovered, all uses of the global show up
as references in Ghidra.

As a case in point, this does allow relatively easy discovery of most
on-screen-display (OSD) strings once the function writing strings to the
OSD was discovered.

I'd hate to code to this ISA, though.
