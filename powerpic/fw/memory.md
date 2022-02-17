---
title: Memory
parent: Firmware
grand_parent: PowerP/C
---

# Memory

## Memoy Map

| Memory Section | Start Address | End Address |
|:--------|:-----:|:----:|
| Reset Vector | 0x0000 | -- |
| User Memory | 0x0001 | 0x0003 |
| Interrupt Vector | 0x0004 | -- |
| User Memory | 0x0005 | ** |
| User ID | 0x8000 | 0x8003 |
| Rev ID | 0x8005 | -- |
| Dev ID | 0x8006 | -- |
| Config Word 1 | 0x8007 | -- |
| Config Word 2 | 0x8008 | -- |
| Config Word 3 | 0x8009 | -- |
| Config Word 4 | 0x800A | -- |
| Config Word 5 | 0x800B | -- |
| Dev Info | 0x8100 | 0x811F |
| Dev Config Info | 0x8200 | 0x82FF |
| Data Flash Mem (EEPROM) | 0xF000 | 0xF0FF |

*\*\*End Memory Address*
- *PIC16LF19196: 0x3FFF - 16k words (28KB)*
- *PIC16LF19197: 0x7FFF - 32k words (56KB)*

*Note: A single address points to a 14-bit chunk of memory*

**Configuration Words:**\
Configuration words are 14-bits in length and contain configuration for the PIC prior to startup.

**Program Flash Memory:**\
*The datasheet shows that the PIC16LF19196 has memory availble from address 0x0000 to 0x3FFF which is a total of 16,383 memory addresses. I can't seem to find any information on these extra addresses, do they just round to 16k words? To be determined..*

Program flash memory is divided and subdivided into chunks, with the smallest being a:

**Word** - 14 bits, holds an instruction and the needed arguments. Each word is referenced with a 15-bit address.\
64 words combine to form a:

**Row** - 64 words. the smallest chunk of memory you can write. Writes cannot cross row boundaries.\
32 Rows combine to form a:

**Page** - Pages are the largest chunk, holding 2048 words.


**Data Memory (RAM):**\
Data Memory is partitioned into 64 memory banks of 128 bytes each. Uses a 13-bit address.