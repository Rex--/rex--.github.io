---
layout: default
title: Firmware
parent: PowerP/C
permalink: /powerpic/fw
has_children: true
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