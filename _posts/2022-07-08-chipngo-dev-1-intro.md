---
layout: post
title: "CHIPnGo Dev #1 - Intro"
---

Toward the end of last year, I found myself becoming more and more interested in the lower-level details of how a computer actually works. Although I have been programming in C on and off for a few years now, I didn't *really* know what was going on (I mean, I still don't, but I'm getting closer!) and felt that needed to change.

I could have started with a dry textbook that in all likelihood I never would have finished, but I had a different idea. I have been familiar with emulators for years now, having relied on them to replay Pokemon Red for the **n**th time whenever a wave of nostalgia hits me, but I didn't know much about how they work. However, I figured that learning how to write an emulator would be a fun way to learn more about computer architecture and so I set out to get started.

Of course, I had to decide what system to emulate. I originally had dreams of jumping right into something like GameBoy Color or SNES since those were systems I grew up with as a kid. However, after doing some research, I learned that might be a bit too ambitious for my first emulator so instead I started with a very simple system, considered the "Hello, world!" of emulators: [CHIP-8](https://en.wikipedia.org/wiki/CHIP-8).

Now it's important to note that CHIP-8 never existed as a physical system, so the term *emulator* here is a bit of misnomer. More accurately, it could be described as a virtual machine or interpreted language. It was originally created in the 1970s by Joseph Weisbecker to make game programming for the [COSMAC VIP](https://en.wikipedia.org/wiki/COSMAC_VIP) simpler and consisted of a mere 35 opcodes.

<p align="center">
<img src="/assets/img/cosmac.jpg" width="50%" height="50%" />
<small>The COSMAC VIP - A thing of beauty!</small>
</p>

However, this is why it's widely considered a good system for beginners to emulate. Despite not technically being a physical system, you are still introduced to the idea of opcodes and assembly language, as well as basic computer architecture such as stacks, registers, and program counters.

Now I don't want to bore you with more details than you probably wish to know about CHIP-8, but needless to say it can be difficult to make an emulator that supports as many CHIP-8 ROMs as possible because there are several different variations of CHIP-8 out there each with their own quirks. However, after many hours of debugging and testing, I finally came up with a semi-polished, halfway-decent CHIP-8 emulator which I call [JAXE](https://github.com/kurtjd/jaxe).

<p align="center">
<img src="/assets/img/brix.gif" width="75%" height="75%" />
<small>"Brix" running on JAXE</small>
</p>

Anyway, fast forward to the beginning of this year where I finally started my freshman year formally studying Computer Science. I had also joined my school's robotics club around the same time and found myself out of my element, having never touched a microcontroller before. So, I had to learn real quick how to code without an operating system there holding my hand.

After a couple months of this, I finally felt I had a basic understanding of things and had become so interested in embedded software that I changed my major to Electrical & Computer Engineering.

Though I still only felt like I was scratching the surface when it came to embedded development, and wanted to dive deeper. Thus, I needed a project to push my comfort zone. Seeing as CHIP-8 never existed as an actual game console, I figured it would be pretty cool to make that a reality, so that's what I set out to do.