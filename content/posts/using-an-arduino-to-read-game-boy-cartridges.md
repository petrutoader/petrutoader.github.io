---
title: "Using an Arduino to read Game Boy cartridges"
date: 2018-08-31T10:16:44+02:00
categories: [gameboy, arduino]
draft: false
---

# Intro

This is a blog post part of my project to build a flashcart for the Game Boy, you can read the [previous post here](/post/how-do-gameboy-cartridges-work).

# Overview

There are two main components needed in order to build this software:

* The Arduino's software, to manipulate to ports so that we can read/write to ROM/SRAM
* The software on the computer to read the data gathered by the Arduino

The reason behind why we need to use software on the computer is because the Arduino doesn't have enough memory to hold all types of Game Boy cartridges, so either we attach some sort of SD card shield, either we send all data via Serial port to the computer.

I'm thinking of building a GUI for the software on the computer that will be able to tell the Arduino what operations to run.

# Arduino's software

I have published the code for the reader [on GitHub](https://github.com/petrutoader/gb-reader), please take a look and if you find anything that could be improved, please do so by contributing to it!

We'll take a brief look at the code, from the start I can tell you that the files we should be looking at right now are:

* src/main.cpp — main implementation
* include/GBIO.h — header file for the GBIO library
* lib/GBIO.cpp — GBIO library

I have written the GBIO library that will help you implement the reading/writing for whatever cartridge you want. At the moment, the only assumptions it takes is the connections to digital and analog pins on the Arduino, but I'm looking forward to changing that so that you can specify which ports to use yourself.

I won't be going through each line of the code, I leave that to the reader if you want, as the code is rather simple. The core idea behind it is the following:

## Pin usage

I'll briefly describe the basic operations we'll be doing, you can read more about the pins [in the previous post](/post/how-do-gameboy-cartridges-work).

* Send the address you want to access via A0-16 pins
* Read/write via the D0-7 pins
* Control operation types via WR, RD, CS_SRAM pins

## Read the ROM

Reading the ROM was the first thing I implemented, it's widely used as it provides not only the game but also important data such as ROM and SRAM bank counts, which you need nevertheless in order to read and write to the SRAM.

* Read the ROM bank count (to see how many times to switch banks via the MBC)
* Iterate through addresses 0x0000 to 0x4000, once you reach the edge address switch the bank to the next one
* Output each byte to the Serial using the Arduino

## Read the SRAM

* Read the SRAM bank count (to see how many times to switch banks via the MBC)
* Enable SRAM reading (separate instruction)
* Iterate through 0xA000 to 0xC000, once you reach the edge address switch the bank to the next one
* Output each byte to the Serial using the Arduino

_Note: Some different carts might have different SRAM edge addresses._

## Write the SRAM

Here's the fun part, ever wished to have a shiny Celebi on a Pokemon cart? Now you'll be able to.

* Read the SRAM bank count (to see how many times to switch banks via the MBC)
* Enable SRAM reading (separate instruction)
* Input each byte from the Serial using the Arduino
* Put CS_SRAM LOW and then HIGH for each byte written
* Iterate through 0xA000 to 0xC000, once you reach the edge address switch the bank to the next one

# Computer software

The idea behind the software running on the computer is the following:

* dump.py — outputs data into a file via Serial from the Arduino, passing command line arguments for the output file
* write_sram.py — inputs data from a file, and sends it via Serial to the Arduino

You will most likely have to write your serial's port device in the files as well as to read the code to understand which command line arguments you need to pass.

# What's next

Well, now we're able to read the ROM and read/write to the SRAM and that's about all you can do with original cartridges, but I think it's time to step our game up and try to create a flashcart ourselves which will allow us to play backed up games.

We'll use one of InsideGadget's flashcart that uses a AM29F016B flash chip first and after we've got a sucessful cart write to it we can go further and try to make our own flashcart from scratch.

Stay tuned!
