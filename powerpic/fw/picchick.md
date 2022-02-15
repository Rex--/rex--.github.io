---
title: picchick
parent: Firmware
grand_parent: PowerP/C
nav_exclude: true
---

# picchick
similar to `avrdude`

## Programming specifications
***

Five connections are needed for ICSP programming:
- ICSPCLK - Schmitt Trigger Input
- ICSPDAT - Schmitt Trigger Input/Output
- MCLR - Program Mode Select
- Vdd - Power Supply
- Vss - Ground

I will be using the low voltage In-Ciruit Serial Programming (ICSP) Interface to program the Nonvolatile memory (NVM). This includes the Program Flash Memory (PFM), EEPROM, dedicated "User ID" locations and the Configuration Words.

***

### Erasing Memory
PFM is erased by row or in bulk. 'Row' refers to the minimum erasable size (64 words) and 'bulk' is one of the many possible subsets of all memory rows. All Bulk Erase ICSP commands have a higher minimum Vdd requirement than the Row Erase commands. The duration of an erase is always determined internally.


### Writing Memory
PFM is writting one row at a time. Multiple Load Data ICSP commands are used to fill the row data latches. The duration of the write is determined either internally or externally by a Begin Internally Timed or a Begin Externally Timed command.
PFM panels include a 64-word (1 row) programming interface. The row to be programmed must first be erased, either with a Row Erase or Bulk Erase command. To modfy only a a portion of a previously programmed row, the contents of the entire row must first be read, then the new data and the retained data can be written into the write latches to reprogram the row of PFM.

Checkout the [Memory](/powerpic/fw/memory.html) page for addressess.
***

### Programming algorithm
The device uses internal latches to temporarily store the 14-bit words used for programming. The data latches allow the user to program a full row (64-words) with a single Begin Programming command. The Load Data command is used to load a songle data latch(word). The data latch will hold the data until the Begin Programming command is given.

Data latches are aligned with thte LSBs of the address. The address at the time the Begin Programming command is given will determine the memory row is written. Writes cannot cross a physical row boundary. If more than the maximum number of latches are written without a Begin Programming command, the data in the latches will be overridden. 

PFM is programmed one row (64 words) at a time.\
User ID and Config Words are programmed one word at a time.\
EEPROM or Data Falsh Memory is programmed one byte at a time.

***

### Intel Hex File

A [intel hex file](/powerpic/docs/INHEX.pdf) contains records that begin with an ascii ':' or '0x3A' in hex.

An example record is `:02000E00E83FC9`\
Each record contains:
1. Record mark: 1B - `:`
2. Length of data: 1B - `02`
3. Load Offset: 2B - `000E`
4. Record Type: 1B - `00`
5. Data or Info: nB - `E83F`
6. Checksum: 1B - `C9`

| Record Types | Byte Code |
|:--------|:-----------:|
| Data | 0x00 |
| EOF | 0x01 |
| Ext Seg Addr | 0x02 |
| Start Seg Addr | 0x03 |
| Ext Linear Addr | 0x04 |
| Start Linear Addr | 0x05 |

**Checksums**

From the 1988 INHX32 Datasheet:\
    The checksum is the ascii representation of the two's compliment of the 8-bit bytes that result from converting each pair of ascii hexadecimal digits to one byte of binary, from and including the Length field to and including the last byte of the Data or Info field. Therefore, the sum of all the ascii pairs in a record after converting to binary, from the Length field to and including the Checksum field, is zero.\
Basically, compute the checksum by summing up all the values from Length of Data to Data, take the LSB and NOT+1 to get the two's compliment. To verify the checksum, sum the enitre record and check if its zero. If not, the record is corrupt.


**Converting hex addresses to PIC Memory Addresses**

In the hex file there are two bytes per program wored stored in the Intel INHX32 hex format. Data is stored LSB first, MSB second. Because there are two bytes per word, the addresses in the hex file are 2x the address in program memory. Configuration words should be read from the hex file. If they are not present, a warning message should be issued.

An example being the Coniguration Words located at address `0x8007` in the PIC memory will be 2x that in the hex file: `0x1000E`.



