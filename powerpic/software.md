---
layout: default
title: Software
parent: PowerPIC
grand_parent: Projects
has_children: true
---

# PowerPIC Software

### Compilation

Compilation is done using `microchip-mplabxc8-bin` from the AUR which installs
version 2.6 of the Microchip's `xc8-cc`. This is used to compile and link the
[firmware]() for the powerpic.

In the future I would like to migrate to the currently unmainted PIC16 branch of the [SDCC](http://sdcc.sourceforge.net).

### Flashing

Flashing is done using [picchick](https://github.com/Rex--/picchick), which consists
of a CLI tool that decodes a hexfile and sends it to a serial device, and some arduino firmware
that takes this decoded data and translates it into PIC's programming interface(ICSP).
