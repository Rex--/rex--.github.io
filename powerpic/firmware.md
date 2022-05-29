---
layout: default
title: Firmware
parent: PowerPIC
grand_parent: Projects
has_children: true

nav_exclude: true # Page WIP
---

# PowerP/C Firmware
This page will describe the firmware for the powerpic. Firmware is defined as the software that runs on the actual PIC

## Oscillator Module

Available Clock sources:
- HFINTOSC: 1-32 MHz
- LFINTOSC: 31 kHz

Configure the clock source by programming the RSTOSC Configuration bits, or manipulate the NOSC<2:0> bits in the OSCCON1 register during runtime.

**High Frequency Internal Oscillator (HFINTOSC)**

Select the frequency of the HFINTOSC with the HFFREQ<2:0> bits of the OSCFREQ register. The NDIV<3:0> bits of the OSCCON1 register allow for divison of the HFINTOSC from 1:1 to 1:512. Use the OSCTUNE register to tune the HFINTOSC.

**Low Frequency Internal Oscillator (LFINTOSC)**

## Interrupts

Interrupts are disabled upon device Reset. They are enabled by setting the following bits:
- GIE bit of the INTCON register
- Interrupt Enable bits of the PIEx[y] registers for the specific interrupt events
- PEIE bit of the INTCON register (if the Interrupt Enable bit of the interrupt event is contained in the PIEx registers)

The PIR0-PIR8 registers record individual interrupts via interrupt flag bits. The firmware within the Interrupt Service Routine (ISR) should determine the source of the interrupt by polling the interrupt flag bits. The interrupt flag bits should be cleared before exiting the ISR to avoid repeated interrutps.



**Useful Links**
- [PIC16(L)F1919x Datasheet](/powerpic/docs/PIC16LF1919X-Datasheet.pdf)
- [PIC16(L)F1919x Memory Programming Specification](/powerpic/docs/PIC16LF1919X-Memory-Programming-Spec.pdf)
- [MPLAB XC8 C Compiler User's Guide for PIC](https://www.microchip.com/content/dam/mchp/documents/DEV/ProductDocuments/UserGuides/50002737D.pdf)
- [MPLAB XC8 PIC Assembler User's Guide](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20PIC%20Assembler%20User%27s%20Guide%2050002974A.pdf)
- [MPLAB XC8 User's Guide for Embedded Engineers](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20C%20Compiler%20UG%20EE%20DS50002400C%20.pdf) 