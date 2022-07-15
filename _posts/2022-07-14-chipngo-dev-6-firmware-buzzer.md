---
layout: post
title: "CHIPnGo Dev #6 - Firmware: Buzzer"
---

While implementing sound for my emulator on desktop I had become familiar with the basics of digital sound. I knew I needed to generate a square wave with a 50% duty cycle (for my case at least) at some frequency (I was aiming for around 440Hz).

So after some research it seemed pulse-width modulation (PWM) was the way to accomplish this, which luckily the bluepill supports. Since CHIP-8 only ever supports a single frequency, it was as simple as setting up the initial conditions according to the datasheet and then starting and stopping the signal at the appropriate times.

I wish I had more to add to this but I didn't really run into too many challenges. In the future if I support XO-CHIP I'll need to dive deeper into PWM and digital sound, but for now you can take a look at my simple [PWM library](https://github.com/kurtjd/CHIPnGo/blob/main/src/pwm.c).

<p align="center">
<img src="/assets/img/chipngo-breadboard-2.jpg" width="100%" height="100%" />
<small>I decided briefly to combine two breadboards at this stage for space but later went back to just the one.</small>
</p>

<br>
<table align="center">
<tr>
<td><a href="/2022/07/13/chipngo-dev-5-firmware-buttons"><< Prev</a></td>
<td><a href="/2022/07/14/chipngo-dev-7-firmware-display">Next >></a></td>
</tr>
</table>