---
layout: default
title: Keypad
parent: Hardware
grand_parent: PowerP/C
---

## Keypad
This section will describe the 4 x 4 key matrix on the front of the watch.
The general idea is that the rows will be configured as outputs and the columns as inputs. By driving the row low and scanning the columns we can decide which button is being pressed. The column pins have the ability to generate an interrupt on logic level change, so we can use this feature to only start the column scanning when one gets pulled low.

    TODO: Learn how a T9 keypad is set up.....

## Side Buttons
This section will describe the two side buttons. It might get moved to its own page, but right now its just a stub.

The pins connected to the side buttons also support interrupt on change, so we will use this feature to detect when they are pressed.
