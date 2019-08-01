---
title: "nand2tetris, building a modern computer from NAND gates"
date: 2018-10-15T10:16:44+02:00
categories: [study, software]
draft: false
---

# Motivation

I never studied software engineering in an academic setup, but somehow I got to the point in which I was confident with my software development skills. Obviously I wasn't doing anything too fancy but it was enough for me. Recently as you can see from my blog I started getting into electrical engineering. I discovered that I had a huge knowledge gap, as I didn't understand how the electrical part of the computers I was coding for was working, nor did I know how they functioned on a really low level. So luckly [vladh](http://vladh.net) recommended me this course, and I loved it.

# About the course

Around 8 weeks ago I started [nand2tetris](https://nand2tetris.org), a fantastic course that I took on Coursera (called there "Build a Modern Computer from First Principles: From Nand to Tetris") tought by two professors from Hebrew University of Jerusalem.

The course takes you on a 6 week trip from building XOR, OR, AND, etc. chips using solely a NAND gate using HDL (Hardware Description Language) up until you've built using those gates an ALU (Arithmetic Logic Unit) and further a CPU that can handle different types of operations (keyboard reading as well as displaying stuff on a virtual screen).


# Writing an assembler

In the last week you get to write an assembler for the PC you've built so you don't have to write machine code for it. I've done this initially in Python, but I'm now rewriting it in C as I'm currently learning the language.

You can see the progress that I've done by checking out the [repository of the assembler](https://github.com/petrutoader/hack-assembler).


# Writing an operating system

I'm currently thinking of starting the next course of the series which goes a lot deeper into the software engineering side of building a computer. From the reviews I saw that it's a ton of work and I have to make sure I have time planned for it. The main idea behind it is to get to the point in which you can play tetris on the computer, obviously for that you'll need a lot more than a simple assembler.
