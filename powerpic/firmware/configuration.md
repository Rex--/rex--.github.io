---
title: Configuration
parent: Firmware
grand_parent: PowerPIC
---

# Configuration of the PIC
Configuratione for the PIC is done in two ways:
1. Configuration Words are set when the PIC is programmed with a hex file. These Configuration Words define startup settings for the PIC.
2. Congiuration Registers are set during runtime by the firmware. They are read/writeable while the PIC is running, allowing various peripheral settings to be changed.

***

## Configuration Words

**Configuration Word 1: *Oscillators***

* bit 10: LCDPM: LCD Charge Pump Mode
    - 1 = Enables during LCD operation *Default*
    - 0 = Forced off 

* bit 6-4: RSTOSC<2:0>: Power-up Default values for COSC bits (ReSeT OSCillator?)
    - 111 - External Oscillator *Default*
    - 110 - HFINTOSC, `HFFRQ=0b0000`4MHz, CDIV=4:1
    - 101 - LFINTOSC
    - 011 - HFINTOSC, OSCFREQ=4MHz, CDIV=1:1
    - 000 - HFINTOSC 2x PLL `HFFRQ=0b1111` 32MHz, CDIV=1:1

**Configuration Word 2: *Supervisors***

* bit 9: BORV: Brown-out Rest Voltage Selection Bit
    - 1 = Lower trip point *Default*
    - 0 = Higher trip point *Note: Higher trip is recommend for  >16MHz

* bit 7-6: BOREN<1:0>: Brown-out Reset Enable Bits
    - 11 = Enabled, SBOREN is ignored
    - 10 = Enabled while running, Disabled in sleep, SBOREN is ignored
    - 01 = Enabled according to SBOREN
    - 00 = Disabled

* bit 5: LPBOREN: Low-Power BOR Enable Bit

**Configuration Word 3: *Windowed Watchdog***

* bit 6-5: WDTE<0:1>: Watchdog Operating Mode
    - 00 = WDT Disabled, SWDTEN is ignored
    - 01 = WDT enabled/disabled by SWDTEN bit in WDTCON0
    - 10 = WDT enabled while Sleep = 0, suspended when Sleep = 1; SWDTEN ignored
    - 11 = WDT enabled regardless of sleep, SWDTEN is ignored

***

## Configuration Registers

***INTCON*** - Interrupt Control Register
* bit 7: GIE - Global Interrupt Enable Bit
  - 1 = Enables all active interrupts
  - 0 = Disables all interrupts
* bit 6: PEIE - Peripheral Interrupt Enable Bit
  - 1 = Enables all active peripheral interrupts
  - 0 = Disables all peripheral interrupts

***PIE0*** - Peripheral Interrupt Enable Register 0
* bit 4: IOCIE: Interrupt-on-Change Interrupt Enable Bit
  - 1 = Enables the IOC  interrupt
  - 0 = Disables the IOC interrupt

***PIE3*** - Peripheral Interrupt Enable Register 3
* bit 5: RC1IE: USART Receive Interrupt Enable Bit
* bit 4: TX1IE: USAT Transmit Interrupt Enable Bit

***PIE8*** - Peripheral Interrupt Enable Register 8
* bit 7: LCDIE: LCD Interrupt Enable Bit

***PIR0*** - Peripheral Interrupt Status Register 0
* bit 4: IOCIF: Interrupt on Change interrupt Flag Bit *Note: Read-Only - This is the logical OR of all the IOCAF-IOCEF flags. Clear those to clear this*
  - 1 = One or more of the IOCAF-IOCEF register bits are currently set, indicating an enabled edge was detected
  - 0 = None of the IOC register bits are set

***PIR3*** - Peripheral Interrupt Status Register 3
* bit 5: RC1IF: EUSART1 Receive Interrupt Flag *Note: Read-Only*
  - 1 = The EUSART recieve buffer is not empty (Contains at least one byte)
  - 0 = The EUSART recieve buffer is empty
* bit 4: TX1IF: EUSART1 Transmit Interrupt Flag *Note: Read-Only*
  - 1 = The EUSART1 transmit buffer contains at least on empty space (bit?)
  - 0 = The EUSART1 transmit buffer is full

***PIR8*** - Peripheral Interrupt Status Register 8
* bit 7: LCDIF: LCD Interrupt Flag bit
  - 1 = A LCDIF interrupt has occured (Must be cleared in software)
  - 0 = No LCDIF interrupt has occured
* bit 6: RTCCIF: Real-Time-Clock-Calender Interrupt Flag bit
  - 1 = A RTCCIF interrupt has occured
  - 0 = No interrupt has occured

## I/O Ports
Each port has 10 standard registers:
- PORTx - reads the level on the pin
- LATx - output latch
- TRISx - data direction
- ANSELx - analog select
- WPUx - weak pull-up
- INLVLx - input level control
- SLRCONx - slew rate
- ODCONx - open drain

Port shortnames are R(x)(y) with x being the port number and y being the bit identifier. E.g. for PORTA 1 - RA1

## Peripheral Pin Select (PPS) Module
Select piins for different Peripherals\
*TODO*

## Peripheral Module Disable
Provides the ability to disable unused modules for lower power consumption.\
*TODO*