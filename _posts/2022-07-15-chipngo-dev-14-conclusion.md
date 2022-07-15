---
layout: post
title: "CHIPnGo Dev #14 - Conclusion"
---

As I was working on CHIPnGo and learning more about embedded development, there were many times I realized I made some sub-optimal design decisions earlier in the development process. Thus, I just want to touch on some of the things I would do differently in future projects.

As I was developing the firmware, I was also learning about the different peripherals the STM32 MCU provides as well. For example, I'd `#define` a lot of volatile pointers to direct registers that I planned to manipulate for the specific driver I was working on. This often led me to having the same pointer defined in multiple files as I realized occasionally two drivers needed to access the same register.

In the future I'd try to factor these out into a common file that multiple drivers can `#include` for use. Better yet, I should take a hint from how some of the HALs out there work by implementing structs that map to the memory of a peripheral so that each member of that struct is automatically mapped to the respective registers of that peripheral. And realistically, it might make sense to just use vendor-provided HALs for future projects as, while looking up memory addresses in the data sheet was a good learning experience, it can be a bit tedious to do over and over.

Also, I have a habit of using magic numbers while I'm writing and testing code and then going back and replacing them with constants to increase readability. However, this proved to be a bad idea for bit manipulation of registers because once I baked in a bitwise operation, I'd immediately forget what it was referencing. Thus, if I wanted to factor it out into a constant, I'd have to go back into the data sheet to jog my memory but this is a real pain. I should have just done this from the start, and while I got better about this later in the project, there are still tons of these magical bitwise operations floating around that I don't really feel like fixing such as this:

```c
// If desired speed is over 36 mhz, set APB1 divider to 2
if (mhz > 36) {
    RCC_CFGR |= (4 << 8);
    APB1_CLOCK_SPEED = (mhz / 2) * MHz;
} else {
    RCC_CFGR &= ~(0x07 << 8);
    APB1_CLOCK_SPEED = mhz * MHz;
}
```

<small><center>I have no idea what ~(0x07 << 8) means anymore.</center></small>

I'd also like to go deeper into general computer architecture and learn more about the peripherals the MCU provides. For example, although I wrote the C code myself, I'm still reliant on the auto-generated startup and linker scripts. It would be nice to know exactly how the MCU lays everything out in memory and actually executes `main()` and begins executing instructions from flash. Not to mention, I still have a very surface-level understanding of things like SPI and UART and I may not have been using them as efficiently as I could have.

As far as the electrical side of things, this project really highlighted how little I know. I honestly felt like I just hacked everything together without really understanding the electrical characteristics and got lucky. I'm currently now trying to teach myself the basics of theoretical/practical electronics (which I hope I'll also learn more about in school as I advance through my education) so that way I can actually feel like I'm making purposeful design decisions as opposed to basically guessing in future projects.

My hope is that now that I'm armed with more knowledge and experience, I can make more polished projects. Since this was meant more as a learning experience, things are quite rough around the edges. I'd like to revisit this project in perhaps a year or so and see if I can improve upon it (possibly supporting XO-CHIP in the process) and perhaps at least approach semi-consumer-grade in quality.

Overall, I'm quite proud with how much I learned over the past few months. I now have a better understanding of how a MCU interacts with hardware, how communication protocols such as UART and SPI work at a basic level, the basics of voltage, current, and power, and a general idea of how to design PCBs. For now I'm just going to focus on learning, but I should have another project in the works soon, so stay tuned!