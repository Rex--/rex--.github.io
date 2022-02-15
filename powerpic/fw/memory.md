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
*TODO*

**Data Memory (RAM):**\
Data Memory is partitioned into 64 memory banks of 128 bytes each. Uses a 13-bit address.