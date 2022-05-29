---
title: picchick
parent: Software
grand_parent: PowerPIC
nav_exclude: true
---

# picchick

picchick is the softwares that implement uploading a .hex file to the PIC. It is composed of firmware that runs on an AVR microcontroller (Atmega32u4(Arduino Micro)) and accompanying software running on the host computer.

**picchick Firmware**\
This should receive some binary data over UART from the Software. This data includes the bytes to be written and the address to be written to. It should decode this into ICSP commands and use them to program the PIC. 

**picchick Software**\
This should read in a hex file, decode it into binary and PIC memory addresses, and send it over UART to the Firmware.

***

## Programming specifications

Five connections are needed for ICSP programming:
- ICSPCLK - Schmitt Trigger Input
- ICSPDAT - Schmitt Trigger Input/Output
- MCLR - Program Mode Select
- Vdd - Power Supply
- Vss - Ground

I will be using the low voltage In-Ciruit Serial Programming (ICSP) Interface to program the Nonvolatile memory (NVM). This includes the Program Flash Memory (PFM), EEPROM, dedicated "User ID" locations and the Configuration Words.

**ICSP Commands**

| Command | Byte Code | Payload Expected |
|:------------|:-----------:|:--------------------:|
| Load Address | 0x80 | Yes |
| Increment Address | 0xF8 | No |
| Bulk Erase | 0x18 | No |
| Row Erase | 0xF0 | No |
| Load Data Inc | 0x02 | Yes, PC incremented |
| Load Data | 0x00 | Yes, PC unchanged |
| Begin Internally Timed | 0xE0 | No |
| Begin Externally Timed | 0xC0 | No |
| End Externally Timed | 0x82 | No |

***

### Erasing Memory
PFM is erased by row or in bulk. 'Row' refers to the minimum erasable size (64 words) and 'bulk' is one of the many possible subsets of all memory rows. All Bulk Erase ICSP commands have a higher minimum Vdd requirement than the Row Erase commands. The duration of an erase is always determined internally.


### Writing Memory
PFM is written one row at a time. Multiple Load Data ICSP commands are used to fill the row data latches. The duration of the write is determined either internally or externally by a Begin Internally Timed or a Begin Externally Timed command.
PFM panels include a 64-word (1 row) programming interface. The row to be programmed must first be erased, either with a Row Erase or Bulk Erase command. To modfy only a a portion of a previously programmed row, the contents of the entire row must first be read, then the new data and the retained data can be written into the write latches to reprogram the row of PFM.

The Load Data command is used to load one programming data latch: one 14-bit instruction for PFM/Configuration Words/User ID, or one 8-bit byte for EEPROM.



Checkout the [Memory](/powerpic/firmware/memory.html) page for addressess.

***

### Programming algorithm
All commands and data words are transmitted Most Significant bit first. Data changes on the rising edge of the clock and is latched on the falling edge. Both DAT and CLK are Schmitt Trigger inputs. Upon entering programming mode, all logic is placed into the Reset state and all pins are automatically configured as high impedance inputs.

The device uses internal latches to temporarily store the 14-bit words used for programming. The data latches allow the user to program a full row (64-words) with a single Begin Programming command. The Load Data command is used to load a single data latch(word). The data latch will hold the data until the Begin Programming command is given.

Data latches are aligned with the LSBs of the address. The address at the time the Begin Programming command is given will determine the memory row that is written. Writes cannot cross a physical row boundary. If more than the maximum number of latches are written without a Begin Programming command, the data in the latches will be overridden. 

PFM is programmed one row (64 words) at a time.\
User ID and Config Words are programmed one word(14-bits) at a time.\
EEPROM or Data Flash Memory is programmed one byte at a time.

***

### Intel Hex Files
An [intel hex file](/powerpic/docs/INHEX.pdf) contains records that begin with an ascii ':' or '0x3A' in hex.

An example record is `:02000E00E83FC9`\
Each record contains:
1. Record mark:  `:` - 1 byte
2. Length of data: `02` - 1 byte
3. Load Offset: `000E` - 2 bytes
4. Record Type: `00` - 1 byte
5. Data or Info: `E83F` - n bytes
6. Checksum: `C9` - 1 byte

| Record Types | Byte Code |
|:--------|:-----------:|
| Data | 0x00 |
| EOF | 0x01 |
| Ext Seg Addr | 0x02 |
| Start Seg Addr | 0x03 |
| Ext Linear Addr | 0x04 |
| Start Linear Addr | 0x05 |

**Checksums**\
From the 1988 INHX32 Datasheet:\
    The checksum is the ascii representation of the two's compliment of the 8-bit bytes that result from converting each pair of ascii hexadecimal digits to one byte of binary, from and including the Length field to and including the last byte of the Data or Info field. Therefore, the sum of all the ascii pairs in a record after converting to binary, from the Length field to and including the Checksum field, is zero.\
Basically, compute the checksum by summing up all the values from Length of Data to Data, take the LSB and NOT+1 to get the two's compliment. To verify the checksum, sum the enitre record and check if its zero. If not, the record is corrupt.


**Converting hex addresses to PIC Memory Addresses**\
In the hex file there are two bytes per program wored stored in the Intel INHX32 hex format. Data is stored LSB first, MSB second. Because there are two bytes per word, the addresses in the hex file are 2x the address in program memory. Configuration words should be read from the hex file. If they are not present, a warning message should be issued.

An example being the Coniguration Words located at address `0x8007` in the PIC memory will be 2x that in the hex file: `0x1000E`.



