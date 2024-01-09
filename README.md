# gba-bios

A Game Boy Advance replacement BIOS. Written completely in ARM assembly from scratch.

![Boot Screen](screenshot.png)

### Accuracy

This is mostly a fun project by two programmers, therefore high accuracy is a non-goal.\
But we aim to make it compatible with most commercial games.

### TODO

- implement remaining BIOS calls (mainly sound functions)
- handle attempts to read BIOS memory in all BIOS calls
- test BIOS against more games / fix bugs

---

## Notorious Beeg Changes

In my emulator, i wanted to completely HLE the bios, however, handling correct bios openbus valus was very difficult, amoungst other things.

The idea i was to change BIOS so that when it enters an irq, it writes the value of openbus to my emulator, and when leaving irq, it again writes the new value of openbus.

These writes are done via writing to an unused IO port. A magic value is also assigned to r11 to ensure this write was from the bios, and not by accident via a badly coded game.

## Usage

The bios will do a 16-bit write to an unused IO reg `0x04000286` with a command. To check that the write was from this bios, check these 2 register values.

- r10 = magic (0x53365336)
- r11 = addr (0x04000286)

### HLE_CMD_BIOS (0x0)
This command asks your emulator to HLE the bios call, if possible. If your emulator handles the bios call, write `0x1` to r9, otherwise the bios will handle it for you.
- r8 = bios number (0x0 - 0x2A)
- r9 = output (0=!handled, 1=handled)

### HLE_CMD_OPENBUS (0x1)
This command tells your emulator what the current bios openbus value should be.
- r2 = openbus value
