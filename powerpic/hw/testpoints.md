---
title: Test Points
parent: Hardware
grand_parent: PowerP/C
---

# Test Points

The ICSP and UART pins are broken out in 5x 2mm diameter test points on the back of the powerpic. The existing power and ground pads are to be used to power the device while programming. 

## UART
Pins RC2 and RC3 are broken out to provide a debug serial interface during development. I attempted to put them in a place as to not interfere with the casing, but the succes has yet to be determined.

## ICSP
The three needed ICSP pins have been broken out to provide a programming interface to the pic. Both DAT and CLK re-use LCD segment pins, so it will have to be disconnected before programming.

<img src="/powerpic/docs/programming-pinout.svg" width=500em>

1. **RX** - RC2
2. **TX** - RC3
3. **DAT** - RB7
4. **CLK** - RB6
5. **MCLR** - RG5
6. **GND** (Middle Pad)
7. **VIN** (Top Pad)