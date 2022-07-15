---
layout: post
title: "CHIPnGo Dev #5 - Firmware: Buttons"
---

The first thing I figured I'd tackle would be wiring up the buttons as they'd likely be pretty straight-forward. After writing a few peripheral libraries while porting the emulator over (UART, system clock, etc) I had gotten decent at reading the data sheet so grokking basic GPIO wasn't too bad.

However there were some tricky parts. Based on some previous research I knew I'd need pull-up resistors (to prevent random noise from being read by a floating pin) and have a way of dealing with switch bounce. Initially I just used physical pull-up resistors but I quickly learned that the bluepill has built-in PURs so I was able to activate those instead.

I mainly handled bounce via software by checking the elapsed time in ms since the last button interrupt, and if it was less than 5ms (listed in the button data sheet as max contact bounce) simply do nothing. This seemed to work fine for the most part, however it's hard to tell if the issues I was having were caused by bounce or something else (which I'll get into shortly).

Another thing I wanted was for a way to detect when a button is released (as opposed to just being pressed). A simple press check can be easily determined by polling the GPIO register containing the pin the button is connected to or via interrupt, but a release check seemed more complicated and I couldn't really find any simple solution out there already.

So in my first iteration, which I was using polling to check if buttons were pressed or not, I basically had an array of each button's state (where each element could be in the UP, DOWN, or RELEASED state). Every time a button was polled, it would check its actual current state and compare it with the state saved in the array. For example, if the button was previously in the DOWN state, but a poll of the GPIO register shows the button is not pressed, we know the button has just been RELEASED and update the array with that state.

But I wasn't too happy with this because one, it felt kind of hacky, and two, more occasionally then I would've liked button presses or releases weren't getting registered. So I scrapped the use of polling and went with an [interrupt driven design](https://github.com/kurtjd/CHIPnGo/blob/main/src/buttons.c) instead.

Unfortunately, it seems that once you intiialize the GPIO interrupt to be triggered by both falling (a press) and rising (a release) edges, there is no way to tell which is happening when the interrupt actually gets triggered. So, I couldn't just check for a rising edge and know immediately if a button was released. I had to keep the array of button states and have an the interrupt check the previous state of the button to determine if the button has been released or pressed.

This worked a bit better, but it's still not perfect. I will probably investigate more in the future, but at this point I decided to continue on with the project as I felt it was "good enough".

<p align="center">
<img src="/assets/img/chipngo-breadboard-1.jpg" width="100%" height="100%" />
<small>Current state of the project (with unrelated placeholder display while I waited for the actual one to arrive).</small>
</p>

<br>
<table align="center">
<tr>
<td><a href="/2022/07/13/chipngo-dev-4-firmware-porting-emulator"><< Prev</a></td>
<td><a href="/2022/07/14/chipngo-dev-6-firmware-buzzer">Next >></a></td>
</tr>
</table>