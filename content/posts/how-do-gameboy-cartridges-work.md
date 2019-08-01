---
title: "How do Game Boy cartridges work?"
date: 2018-08-16T11:23:44+02:00
categories: [gameboy, arduino]
draft: false
---

# Brief intro and motivation

I grew up playing Game Boy games, I always wished to get into console hacking after seeing what [Dark_AleX did on the Playstation Portable](https://en.wikipedia.org/wiki/PlayStation_Portable_homebrew#Dark_AleX), but never actually tried to do so.

I recently got into Electrical Engineering by messing around with Arduinos and I thought that the standard tutorials/challenges you have on the Arduino are really cool but don't actually produce anything useful in the end. I collect in my spare time retro consoles and video games (fixing and refurbishing them).

I thought I could use what I know in order to build a Game Boy game, and use the Arduino in order to flash it to a cartridge. I will write a series of posts on this blog explaining how I'm achieving this, but first things first, let's understand a little bit how Game Boy cartridges work.

# Cartridge components

Let's first look at this picture of a Game Boy cartridge PCB

![Game Boy PCB](/media/how-do-gameboy-cartridges-work/pcb_front.jpg)

You can see in the picture above the following components:

* MBC3 — Memory Bank Controller
* DMG-AW2P-0 — ROM
* BR62256F-70LL — SRAM
* MM1134 — SRAM controller
* Battery

## Memory Bank Controller

As the Game Boy 16bit address bus offers limited space for ROM and RAM handling many cartridges used Memory Bank Controllers in order to expand the available address space by bank switching. Typical to how Nintendo handled hardware for the NES and SNES, the MBC are located in the game cartridge, not in the console. There are multiple MBC types:

* MBC1 (max 2MByte ROM and/or 32KByte RAM)
* MBC2 (max 256KByte ROM and 512x4bits RAM)
* MBC3 (max 2MByte ROM and 64KByte RAM and Timer)
* MBC5 (max 8MByte ROM and 128KByte RAM)

Note: Not all games have a Memory Bank Controller, games under 32KBytes could be fitted inside a simple ROM memory without the need of a MBC.

## ROM

This is where the game is stored, please take note that it is a Read Only Memory, so this is going to give us some small headaches when we'll want to flash another game to the cartridge as part of this series of blog posts.


## SRAM

This is where the saves of the game are stored, again not all games used saves. An example is Bugs Bunny: Crazy Castle 3, which uses a password system in order to return to the level you were playing. Obviously this chip offers R/W support, so we can start our Arduino project by reading and writing to this chip.

## Battery

This is used solely to keep the SRAM alive. As I said I usually collect and refurbish retro video games, so if your cartridge doesn't save the game, then most likely your battery is dead and needs replacement.

# Cartridge pinouts

In order to read from the cartridge we'll have to understand what each pin does.

![Game Boy cartridge pinout](/media/how-do-gameboy-cartridges-work/gb_cartridge_pins.jpg)

* VCC — Power (5V)
* CLK – Clock signal (not used)
* ~WR – if low(grounded) and if RD is low, we can write to the SRAM and load a ROM or SRAM bank
* ~RD – if low (grounded) and if WR is high, we can read the ROM and SRAM
* CS_SRAM – if enabled, selects the SRAM
* A0 – A15 – the 16 addresses lines that we tell the ROM which particular byte of data we want to read
* D0 – D7 – the 8 data lines that we read the byte of data selected by the 16 address lines. These data lines can also be used to control which ROM bank to load (important for later).
* Reset – needs to be connected to VCC
* Audio in – (not used)
* GND – Ground

# Reading the Game Boy Cartridge

In order to read the cartridge we'll need a way for getting inputs and outputs into the pins of the cartridge, for that I chose the Arduino, since it's pretty well documented and easy to use. It's important to know that due to the large amount of GPIOs we'll need something a little more beefy than the Arduino Uno, so I chose the Arduino Mega, featuring 52 digital pins.

## Replacement components

First we'll need some sort of reading header, you can get replacement parts really easily from AliExpress, I have ordered [a bunch of these](https://www.aliexpress.com/item/For-Nintendo-DS-NDSL-GBA-Game-Cartridge-Card-Reader-Slot-Repair-Part/32679592016.html?spm=a2g0s.9042311.0.0.66584c4dy2PlKE) in order to read the Game Boy cartridges.

Sadly this doesn't come with a datasheet so I had to grab my trustworthy Mitutoyo digital caliper and take measurements for each tiny part of it. It's important to have a good measuring tool as resending your designs multiple times because you messed up the measurements can get costly really fast.

## Custom PCBs

I have created a custom PCB that I ordered from [JLCPCB](https://www.jlcpcb.com) for $2 and I've created it using Autodesk's Eagle, which works fine on Windows as well as Mac OS. The most difficult part of it was getting the NDSL GBA Game Cartridge from AliExpress in the right measurements and creating a new library in Eagle.

### Autodesk Eagle

Here's the design of it in eagle:

![Game Boy cartridge breakout shield](/media/how-do-gameboy-cartridges-work/eagle-design.png)

Here you can see how the designs look once built!

![PCBs finished](/media/how-do-gameboy-cartridges-work/pcb-finished.jpg)

### Soldering

Everything worked perfectly from the beginning, you need some basic soldering skills in order to solder this yourself. The only one which is more challenging is getting the cart reader's pins soldered without a SMD soldering station, but you can do it with some extra flux and some courage.

If you do have a SMD soldering station I think you don't really need my advice, but I recommend >T4 paste, T3 won't make good solder points at the size of the pins.

![PCBs soldered](/media/how-do-gameboy-cartridges-work/pcb-soldered.jpg)

_Note: Please ignore the terrible soldering job on the pins, I had no paste :(_

## Software layer

The next part will be starting to use an Arduino in order to connect to the female header pins I just soldered and read/write to it. As I previously wrote I decided to use the Arduino Mega, because it uses the ATMEL ATMega2560 chip, having a ton of GPIO ports.

When we'll get to make something more accessible for an end-consumer we'll be embedding the ATMega2560 chip and getting rid of the Arduino, but for the time being, let's use the Arduino's help.

![PCBs soldered and attached to arduino](/media/how-do-gameboy-cartridges-work/pcb-arduino.jpg)

Now that we've got everything soldered and ready to go, let's go further and add the software layer so that we can start playing around with it!

Next Part: [Using an Arduino to read Game Boy cartridges](/posts/using-an-arduino-to-read-game-boy-cartridges)

# Sources

* [InsideGadget's Arduino Reader](https://www.insidegadgets.com/2011/03/19/gbcartread-arduino-based-gameboy-cart-reader-–-part-1-read-the-rom/)
* [GBDEV on MBCs](http://gbdev.gg8.se/wiki/articles/Memory_Bank_Controllers)
* [Dhole's emulating a Game Boy cartridge](https://dhole.github.io/post/gameboy_cartridge_emu_1/)
