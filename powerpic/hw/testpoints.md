---
title: Test Points
parent: Hardware
grand_parent: PowerP/C
---

# PowerP/C Test Points

The ICSP and UART pins are broken out in 5x 2mm diameter test points on the back of the powerpic. The existing power and ground pads are to be used to power the device while programming. 

### UART
Pins RC2 and RC3 are broken out to provide a debug serial interface during development. I attempted to put them in a place as to not interfere with the casing, but the succes has yet to be determined.

### ICSP
The three needed ICSP pins have been broken out to provide a programming interface to the pic. Both DAT and CLK re-use LCD segment pins, so it will have to be disconnected before programming.

<img src="/powerpic/docs/pogo-pinout.svg" width=500em>

1. **MCLR** - RG5
2. **CLK** - RB6
3. **DAT** - RB7
4. **RX** - RC2
5. **TX** - RC3
6. **GND**
7. **VIN**