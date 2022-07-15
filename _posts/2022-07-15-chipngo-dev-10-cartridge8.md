---
layout: post
title: "CHIPnGo Dev #10 - Cartridge8"
---

As I mentioned previously, having to load games onto the SD card manually would have been a real pain with the way metadata is stored. So, I wanted to create a somewhat polished tool to make this simple since people other than myself might end up using it. I also wanted to include a GUI since a command-line tool probably would have been a bit clunky.

I decided I would use Python with the [Dear PyGui](https://github.com/hoffstadt/DearPyGui) library as there was no sense in wasting time in C for something like this. I don't really have any experience with GUI design so there was a bit of a learning curve, but nothing too crazy. After drawing up a basic design on a whiteboard, I opened up the Dear PyGui docs to figure out how I was actually going to implement it.

To keep things simple, I limited the number of games on the SD card to 25 so I could have a 5x5 grid of tiles representing each game that you could just click and begin editing. This number was pretty arbitrary, and I didn't really need to limit it to this number as I'm sure there is a way to add a scrolling window to the GUI, but it got the job done and looked pretty nice.

<p align="center">
<img src="/assets/img/chipngo-rom-selection.png" width="75%" height="75%" />
<small>Not the fanciest GUI, but at least it's straight-forward.</small>
</p>

Next I began designing the actual game editing window. I also kept this simple, providing a few input boxes for some of the fields and just checkboxes to enable/disable quirks and map buttons to keys.

<p align="center">
<img src="/assets/img/chipngo-rom-edit.png" width="75%" height="75%" />
<small>Right to the point.</small>
</p>

Now that the the GUI was finished, I just had to implement the functionality to store changes to SD. Fortunately, this is quite trivial in Python. You just have to provide the path to the raw SD card and can open it like any other file in binary mode. After that it was just a matter of converting the user input into bytes (and vice-versa when reading game data off the SD card) then seek to the appropriate sector and write the changes.

With this tool complete (which I ended up calling [Cartridge8](https://github.com/kurtjd/CHIPnGo/tree/main/tools/cartridge8)), I could now rapidly store games on the SD and test them on the console. By this point the prototype was nearly complete, I just needed to implement a menu for selecting which game to play on the MCU.