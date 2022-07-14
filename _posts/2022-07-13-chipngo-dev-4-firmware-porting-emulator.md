---
layout: post
title: "CHIPnGo Dev #4 - Firmware: Porting the Emulator"
---

Before I was going to get started wiring up components on the breadboard, I had to first port my emulator to the MCU. Initially this wasn't too difficult, since the emulator was written in C and was already somewhat optimized. This mostly entailed removing the XO-CHIP aspects from the emulator, removing the SDL code for graphics+keyboard, and adding some time-keeping functions which I had originally relied on the operating system to provide.

As I mentioned, I needed some way to keep track of time since the CHIP-8 internal timer runs at a frequency of 60Hz. To accomplish that, I had to take my first step of jumping into a datasheet and try to make sense of things. After a bit of reading, I decided I would use systick to handle this (though I later went with a [hardware timer](https://github.com/kurtjd/CHIPnGo/blob/main/src/clock.c) instead) since it seemed simple enough to implement in code.

And so I wrote up a little systick library which triggered an interrupt every ms so I could track total elapsed ms since program start, which should suffice since my emulator already used elapsed ms in its calculations. The only problem is, I of course had no way of really testing my port since I had no input/output.

Seeing as I had ordered my display from China and it would be several weeks before I got it, I needed to come up with a way to perform testing in the meantime. Since I already had some familiarity with the UART protocol, I had the bright idea of using it to send display data over USB which I'd then have a Python program interpret the data and draw it to screen (and also send back input data).

I wasn't entirely sure this would work as I was worried there was just no way it would be fast enough. I knew right off the bat I'd need to change my current way of storing the display (a bool array of pixels (I was lazy)) to packing 8 pixels into a single byte (thus essentially needing to send 1/8 as many bits every frame).

I also set about pouring through the datasheet to figure out how to set the clock speed of the MCU (so I could run it at its max speed of 72Mhz) which I would likely need anyway in the future for driving the display. After a few hours, I had a [system clock](https://github.com/kurtjd/CHIPnGo/blob/main/src/sysclk.c) library written.

And just a few hours more, I had the [UART library](https://github.com/kurtjd/CHIPnGo/blob/main/src/uart.c) written as well which I was able to test successfully using PuTTY. Lastly, I threw together a [Python script](https://github.com/kurtjd/CHIPnGo/blob/main/tools/serial_interface.py) which used Pygame to draw a display based on the serial data it was receiving.

To my surprise, it was working pretty well! It wasn't blazing fast (mainly because I was drawing the display very inefficiently in Python) but it would be good enough for testing purposes until the physical display would arrive.