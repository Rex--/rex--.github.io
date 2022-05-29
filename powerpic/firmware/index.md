---
layout: default
title: Firmware
parent: PowerPIC
grand_parent: Projects
has_children: true
---

# PowerP/C Firmware


The firmware consists of individual 'mode' applications that are cycled through
with the 'Mode' button. When active, the 'modes' get passed system events including
keypad events, 'Adj' button events, and tick events that occur at a mode defined interval.

The firmware provides libraries with a high level api for these modes to use. The libs
in turn use a small driver to interact with the PIC hardware registers. Eventually, I
hope to implement seperate backend drivers for development.

Checkout the [code on github](https://github.com/Rex--/powerpic/tree/master/firmware)

&nbsp;

&nbsp;


**Useful Links**
- [PIC16(L)F1919x Datasheet](/powerpic/docs/PIC16LF1919X-Datasheet.pdf)
- [PIC16(L)F1919x Memory Programming Specification](/powerpic/docs/PIC16LF1919X-Memory-Programming-Spec.pdf)
- [MPLAB XC8 C Compiler User's Guide for PIC](https://www.microchip.com/content/dam/mchp/documents/DEV/ProductDocuments/UserGuides/50002737D.pdf)
<!-- - [MPLAB XC8 PIC Assembler User's Guide](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20PIC%20Assembler%20User%27s%20Guide%2050002974A.pdf) -->
<!-- - [MPLAB XC8 User's Guide for Embedded Engineers](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20C%20Compiler%20UG%20EE%20DS50002400C%20.pdf)  -->