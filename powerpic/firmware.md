---
layout: default
title: Firmware
parent: PowerP/C
permalink: /powerpic/fw
---

# PowerP/C Firmware
This page will describe the firmware for the powerpic. Firmware is defined as the software that runs on the actual PIC

### Configuration Bits

**Oscillator**
RSTOSC<2:0>:
 - 111 ( Default ) - External Oscillator
 - 110 - HF Internal Oscillator 4MHz
 - 000 - HF Internal Oscillator 32MHs
 - 101 (*) - LF Internal Oscilator

### UART

**`printf`** - Formats the string, then calls `putch()`to send data
This will print to UART, printing to the LCD will be a seperate function. To configure the destination use a global variable to indicate your selection and check it with `putch()`
Note: Must define a `putch(char data)` for printf to work correctly

**Useful Links**
- [PIC16(L)F1919x Memory Programming Specification](https://ww1.microchip.com/downloads/en/DeviceDoc/PIC16F1919X-Memory-Programming-Spec-40001846C.pdf)
- [MPLAB XC8 C Compiler User's Guide for PIC](https://www.microchip.com/content/dam/mchp/documents/DEV/ProductDocuments/UserGuides/50002737D.pdf)
- [MPLAB XC8 PIC Assembler User's Guide](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20PIC%20Assembler%20User%27s%20Guide%2050002974A.pdf)
- [MPLAB XC8 User's Guide for Embedded Engineers](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20C%20Compiler%20UG%20EE%20DS50002400C%20.pdf) 