---
layout: post
title: "CHIPnGo Dev #9 - Firmware: Game Storage"
---

With the SD driver written, I needed to come up with a (semi) elegant way of storing games on the SD if I wasn't going to be using a filesystem. If you recall, CHIP-8 games can be upto 4096 bytes (4kb) (well actually, 3,584 bytes (3.5kb) since the first 512 bytes are reserved by the emulator to store font data and the stack).

Since SD operates on 512 byte sectors, it meant that each game could be given seven sectors (plus one for metadata). So it was simply a matter of locating a game on the SD, then reading in the first sector as metadata, and the subsequent seven sectors directly into the emulator memory.

The challenge was more so in storing and processing the metadata as raw bytes and also deciding when a sector actually contains a game as opposed to garbage. By metadata, I mean things like the game's title, its clock, timer, and display refresh frequency, which emulator quirks should be enabled, and its button mapping.

I first decided that a sector would be identified as containing a game if the very first byte had the value of '0xC8' (short for CHIP-8, very creative I know). I probably should have used more than a single byte as a signature, as the chances of the first byte being 0xC8 by chance are relatively high, but it was good enough for this case.

Next I decided that the following ten bytes would represent the game's title (with trailing 0's if the title was less than ten characters). The bytes were simply interpreted as ASCII values and copied into a string.

The next four bytes would represent the clock speed (I probably could have gotten away with just two bytes since very few games would need to run at a frequency faster than 65KHz, but I had the space). The byte after that would represent the timer frequency, and the byte after THAT would represent the display refresh frequency.

The following byte would represent which emulator quirks were to be enabled. Since there were only eight quirks, this fit into a byte nicely as each bit could represent a corresponding quirk.

Finally, the last twelve bytes would represent the button mapping. Essentially, each button is given two bytes (16-bits) where each bit maps to one of the 16 CHIP-8 keys. So, if the 10th bit was set high for example, that means key A of the emulator would be activated if that button was pressed. This was necessary since I designed the console with only six buttons, but for some games I may want a button to map to more than one key.

This left 482 bytes still free in the metadata sector, which was nice in case I ever want to add more metadata in the future.

But, without some way of streamlining saving games to SD, this would be pretty useless. After all, no one is going to want to write these bytes by hand, so I had to develop a tool to make this more user-friendly.

<br>
<table align="center">
<tr>
<td><a href="/2022/07/14/chipngo-dev-8-firmware-sd"><< Prev</a></td>
<td><a href="/2022/07/15/chipngo-dev-10-cartridge8">Next >></a></td>
</tr>
</table>