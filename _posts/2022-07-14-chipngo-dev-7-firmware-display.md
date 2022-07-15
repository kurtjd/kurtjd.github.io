---
layout: post
title: "CHIPnGo Dev #7 - Firmware: Display"
---

Now that my cheap display finally arrived from overseas, I could begin writing the driver for it and finally move on from using my lousy serial-over-USB hack for testing. But since this was a SPI-driven device, I first had to get familiar with that as I had zero experience with any synchronous communication protocols.

SPI, at a basic level, seemed pretty straight-forward. Unlike UART, where each device needs to ensure they are operating at the same BPS to communicate properly, SPI instead uses a "master-slave" approach where the "master" (in this case the MCU) sends a clock signal directly to the "slave" (the display). Things were made even more simple due to the fact that this communication would only need to be one-way, since this particular display can only receive data, and does not send any information back.

Based on the research I did, I knew it was pretty important to match the SPI clock polarity and phase to what the display expected (basically, the state the clock signal is in when idle and which edge of the clock data is sampled on). And so, I began by initializing the hardware SPI on the MCU to match the requirements of the display driver (ST7567).

It should be noted that at this point in time I did not yet have a logic analyzer or oscilloscope so I had no way of easily seeing what kind of signal I was generating, which means what follows is unfortunately a lot of hacking around and trying different things until I finally got something working.

Anyway, once I felt I had SPI configured correctly on the MCU, I had to start looking at the ST7567 data sheet to figure out how to actually communicate with the thing. First up was performing the reset process, which according to the datasheet was as simple as sending a 0 over the RST line, waiting a few ms, then setting the RST signal to 1.

Next I had to figure out how display data is stored in the display's memory, and how to send commands to the display. It looked pretty confusing at first but after staring at the datasheet for awhile I felt I had it mostly figured out and implemented a basic test function that would just draw a square somewhere on the display.

<p align="center">
<img src="/assets/img/chipngo-display-pixels.png" width="100%" height="100%" />
<small>What the hell is all this?</small>
</p>

Now for the moment of truth. I fired up the MCU hopefully expecting a beautiful white square to pop up on the display and... nothing. CRAP! Without a logic analyer, I had no easy way to determine where I might have went wrong, which means it could have been how I had SPI configured, an incorrect reset procedure, or how I was sending the draw pixel command.

I double-checked my wiring and definitely had wires going from the correct pins on the MCU to the correct pins on the display so that couldn't be it. But being as I don't really know squat about electronics, I feared there was some other electrical issue going on I wasn't aware of. So I looked through the datasheet again and noticed there were a few electrical-related commands, one in particular stood out that allowed you to enable the built-in booster, regulator, and follower.

<p align="center">
<img src="/assets/img/chipngo-display-power.png" width="100%" height="100%" />
<small>Was this the secret ingredient?</small>
</p>

I have no idea what these things mean (and a quick Google search didn't really help much) but I enabled them all anyway to see what would happen, yet still nothing. So I decided I'd take a closer look at how I was initializing SPI and it definitely seemed I was configuring it correctly according to the datasheet, but with not much else to try I began trying different clock phases and polarity just to see what would happen.

Looking at the datasheet, it looked like the clock should be set high when idle and latched on the rising edge, which is how I initally configured SPI. But after combining different polarities and phases, I finally had something show up! Turns out I needed the exact opposite of what the datasheet seemingly showed (clock set low when idle and latched on the falling edge ended up working), so either the datasheet was wrong or I severely misunderstood something (likely the latter since I'm still a noob).

<p align="center">
<img src="/assets/img/chipngo-display-spi.png" width="100%" height="100%" />
<small>Does this not show a clock idle HIGH and rising edge latch?</small>
</p>

With something finally working, I went ahead and removed the power control code, yet to my surprise it stopped working. Huh. So it appears that this may in fact be necessary, yet I'm ashamed to admit I still don't fully understand why. Hopefully as I learn more about electronics I will begin to understand when and why these need to be enabled, but for now I'm okay with it all being black magic in which these secret incantations are necessary for the spell to succeed.

I then began trying to draw more complex images and noticed that, although the display was working, things were quite buggy. So I had to revisit the draw code and iron out the bugs until I had something somewhat stable. Once I was satisfied, I began working on making the emulator's internal display array show up on the physical display.

Things went pretty smoothly, but I was noticing that more often then I'd like the display would completely bug out or stop drawing entirely seemingly at random. Hmmm. I had a hunch that it had something to do with how fast I had set the SPI clock, because I remembered from my experience with UART that with higher baud rates, even if it seemed the CPU was fast enough to support them, there would often be data corruption.

The display data sheet didn't seem to mention a specific clock frequency range that it functions in, so I just began lowering the clock frequency until the bugs stopped. Again, not entirely sure how one would determine a clock frequency outside of trial-and-error, but I'm hoping that as I learn more this will become clear. My only guess is that since SPI is meant for chip-to-chip communication on a board over very small distances, even my ~8in jumper wires were posibly a bit too long to support higher speeds.

Now that everything was in an acceptable state, I wanted a way to write text to the display, mainly for the startup/game selection menu I was planning to implement. I decided on representing each letter and digit as an array of 5 bytes, where each byte represents a row, and the first 5 bits in the byte represent a pixel being on or off for that character (so characters were essentially 5x5 pixels).

To make things easier on myself, I whipped up a quick Python app that used Pygame to present a 5x5 grid letting you click each cell (which represented a pixel) to turn it on or off, effectively allowing you to draw a character, then convert that information into an array of hex digits which I could then copy and paste into the source code. Once I had all letters and digits converted to "bitmaps", all I had to do was write some functions that mapped each character in a string to its correpsonding bits and write those to the display.

<p align="center">
<img src="/assets/img/chipngo-font.png" width="50%" height="50%" />
<small>Drawing the letter 'E'. Nothing fancy, but got the job done.</small>
</p>

At this point I was pretty satisfied overall with the display. My biggest gripe is that there is still heavy "ghosting" (where you can see the last image on the display for enough time to be annoying between frame draws) but I have yet to figure out a way to deal with this. Perhaps, being a very cheap display, there is just no way to fix this. Or perhaps it's an electrical setting that I need to configure, or just a firmware issue. Either way, in the spirit of not being stuck in analysis paralysis, I decided it was acceptable and moved on with the project. The final source code for my display library can be found [here](https://github.com/kurtjd/CHIPnGo/blob/main/src/display.c).

<p align="center">
<img src="/assets/img/chipngo-breadboard-3.jpg" width="100%" height="100%" />
<small>Starting to resemble a game console! (The SD reader here has been wired but not yet functional)</small>
</p>

<br>
<table align="center">
<tr>
<td><a href="/2022/07/14/chipngo-dev-6-firmware-buzzer"><< Prev</a></td>
<td><a href="/2022/07/14/chipngo-dev-8-firmware-sd">Next >></a></td>
</tr>
</table>