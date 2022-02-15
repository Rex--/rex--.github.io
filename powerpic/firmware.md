---
layout: default
title: Firmware
parent: PowerP/C
permalink: /powerpic/fw
---

# PowerP/C Firmware
This page will describe the firmware for the powerpic. Firmware is defined as the software that runs on the actual PIC

## Configuration Words

**1: *Oscillators***

bit 10: LCDPM: LCD Charge Pump Mode
- 1 = Enables during LCD operation *Default*
- 0 = Forced off 

bit 6-4: RSTOSC<2:0>: Power-up Default values for COSC bits (ReSeT OSCillator?)
- 111 - External Oscillator *Default*
- 110 - HFINTOSC, `HFFRQ=0b0000`4MHz, CDIV=4:1
- 101 - LFINTOSC
- 011 - HFINTOSC, OSCFREQ=4MHz, CDIV=1:1
- 000 - HFINTOSC 2x PLL `HFFRQ=0b1111` 32MHz, CDIV=1:1

**2: *Supervisors***

bit 9: BORV: Brown-out Rest Voltage Selection Bit
- 1 = Lower trip point *Default*
- 0 = Higher trip point *Note: Higher trip is recommend for  >16MHz

bit 7-6: BOREN<1:0>: Brown-out Reset Enable Bits
- 11 = Enabled, SBOREN is ignored
- 10 = Enabled while running, Disabled in sleep, SBOREN is ignored
- 01 = Enabled according to SBOREN
- 00 = Disabled

bit 5: LPBOREN: Low-Power BOR Enable Bit

**3: *Windowed Watchdog***

bit 6-5: WDTE<0:1>: Watchdog Operating Mode
- 00 = WDT Disabled, SWDTEN is ignored
- 01 = WDT enabled/disabled by SWDTEN bit in WDTCON0
- 10 = WDT enabled while Sleep = 0, suspended when Sleep = 1; SWDTEN ignored
- 11 = WDT enabled regardless of sleep, SWDTEN is ignored



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


## UART
The operation of the ESUART Module is controlled by three registers:
- TXxSTA - Transmit status and control
- RCxSTA - Recieve status and control
- BAUDxCON - Baud Rate control

Select RX with RXPPS register, TX with RxyPPS.

The USART uses the standard non-return-to-zero (NRZ) format. A Voh Mark state represents a `1` and a Vol Space state represents a `0`. An NRZ transmission port idles in the Mark state. Each character transmission consists of one Start bit (Space), eight or nine Data bits, and one or more Stop bits (Mark). The USART transmits and recieves LSB **first**.

The EUSART module will not remain active during sleep mode since it requires the system clock to operate.

**Transmitter**
The heart of the transmitter is the serial Transmit Shift Register (TSR). The TSR obtains its data directly from the transmit buffer (TXxREG).

To enable the transmitter in asynchronous mode configure these bits:
- TXxSTA: TXEN = 1 - Enables transmitter circuitry
- TXxSTA: SYNC = 0 - Configures EUSART for async mode
- RCxSTA: SPEN = 1 - Automatically configures TX/CK as output. *Note: If the TX pin is shared with an analog peripheral, the analog function must be disabled*

All other EUSART control bits are default values.\
*Note: the TXxIF Transmitter interrupt flag is set when the TXEN bit is set.*

A transmission is initiated by writing a character to the TXxREG. It is held there until all the data in the TSR has been flushed.

The TXxIF flag bit of the PIR3 register is set whenever the EUSART transmitter is enabled and no character is being held for transmission in the TXxREG. The TXxIF is only clear when the TSR is busy with a charcter and a new character has been queued in TXxREG. The TXxIF interrupt can be enabled by setting the TXxIE interrupt enable bit of the PIE3 register. The TXxIF will be set whether the interrupt is enabled or not.

The TRMT bit of the TXxSTA register indicates the status of the TSR register. This read-only bit is set when the TSR is empty and is set when a character is transferred to the TSR from the TXxREG. It remains clear until all data has been shift out of the TSR.

**Set-up Rundown:**
1. Initialize the SPxBRGH, SPxBRGL register pair and the BRGH and BRG16 bits to achieve the desired baud rate.
2. Enable the async serial port by clearing the SYNC bit and setting the SPEN bit.
3. Enable the transmission by setting the TXEN control bit. This will cause the TXxIF interrupt bit to be set.
4. (Optional) Enable the TXxIE interrupt enable bit  of the PIE3 register if interrupts are desired. This will cause an interrupt to occur immediatly provided that the GIE and PEIE bits of the INTCON register are also set.
5. Load 8-bit data into the TXxREG register. This will start the transmission.




**`printf`** - Formats the string, then calls `putch()`to send data
This will print to UART, printing to the LCD will be a seperate function. To configure the destination use a global variable to indicate your selection and check it with `putch()`
Note: Must define a `putch(char data)` for printf to work correctly

**Useful Links**
- [PIC16(L)F1919x Memory Programming Specification](https://ww1.microchip.com/downloads/en/DeviceDoc/PIC16F1919X-Memory-Programming-Spec-40001846C.pdf)
- [MPLAB XC8 C Compiler User's Guide for PIC](https://www.microchip.com/content/dam/mchp/documents/DEV/ProductDocuments/UserGuides/50002737D.pdf)
- [MPLAB XC8 PIC Assembler User's Guide](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20PIC%20Assembler%20User%27s%20Guide%2050002974A.pdf)
- [MPLAB XC8 User's Guide for Embedded Engineers](https://ww1.microchip.com/downloads/en/DeviceDoc/MPLAB%20XC8%20C%20Compiler%20UG%20EE%20DS50002400C%20.pdf) 