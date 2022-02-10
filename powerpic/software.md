---
layout: default
title: Software
parent: PowerP/C
permalink: /powerpic/sw
---

# PowerP/C Software

## Development

Development is done on Arch btw, using `microchip-mplabxc8-bin` from the AUR. This installs version 2.6 of the XC8 compiler.


## Compilation

Currently, Microchip's `xc8-cc` compiler is used to compile and link the [firmware](/powerpic/fw) for the powerpic. A simple Makefile is provided in the [pic-resources](https://github.com/Rex--/pic-resources) repo.

In the future I would like to migrate to the currently unmainted PIC16 branch of the [SDCC](http://sdcc.sourceforge.net).

## Flashing

Flashing is expected to be done by an AVR-based programmer, an arduino will suffice in the meantime. It will use Microchip's low voltage ICSP, which has been broken out into test pads on the bottom of the powerpic board.

A custom board with POGO pins will break out the neccessary connections for programming, alternatively thin wires can be soldered directly to the test points.