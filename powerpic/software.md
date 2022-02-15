---
layout: default
title: Software
parent: PowerP/C
permalink: /powerpic/sw
---

# PowerP/C Software

## Development Stack

Devlopment is currently done in VSCode.

### Compilation

Compilation is done using `microchip-mplabxc8-bin` from the AUR which installs version 2.6 of the Microchip's `xc8-cc`. This is used to compile and link the [firmware](/powerpic/fw) for the powerpic. A simple Makefile is provided in the [pic-resources](https://github.com/Rex--/pic-resources) repo.

In the future I would like to migrate to the currently unmainted PIC16 branch of the [SDCC](http://sdcc.sourceforge.net).

### Flashing

Flashing is expected to be done by an AVR-based programmer, but an arduino will suffice in the meantime. It will use Microchip's low voltage ICSP, which has been broken out into [test points](/powerpic/hw/testpoints.html) on the bottom of the powerpic board.

Firmware for the arduino will need to be written to emulate PIC's ICSP serial interface. Some software running on the PC will be useful to communicate the hex file for programming to the arduino.

The software on the computer that communicates with the programmer device will be called `picchick` in reference to `avrdude`