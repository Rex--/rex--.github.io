---
title: UART
# parent: Firmware
grand_parent: PowerPIC
nav_exclude: true
---

# UART
The operation of the ESUART Module is controlled mainly by three registers:
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