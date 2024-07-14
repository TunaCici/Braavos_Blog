---
title: "AArch64 Procedure Call Standard (AAPCS64): Calling Conventions, Machine Registers & ABI"
tags:
- aarch64
- c
- assembly
- eli5
date: 2024-07-14T00:00:00Z
header_image_alt: "TODO"
---

insert header here

Programming using high-level languages makes a ton of stuff trivial. Your compiler/interpreter abstracts a lot of stuff from you so that you can focus on what's important: getting shit done, efficiently. One of those abstractions is functions.

Calling function is one of the first things you learn when starting programming. They are relatively trivial: you just type the function name, pass some parameters and then it does what it's meant to do (or not). You don't need to think about whether the underlying CPU architecture is 64-bit or that it's by AMD, Intel or Apple. All you care is that the function gets called and successfully returns. That is until you need to do some micro optimizations, debugging on registers or any system-level programming.

insert basic C function call here

At that point it is good to know how the compiler, the standard library and the kernel/OS handles functions. For example, you might need to consider how stack is managed, how are functions called to & returned from, how parameters are passed (via registers or stack). They all depend on your specific platform (e.g., AMD64, AArch64) and the kernel/OS (e.g., Linux, macOS).

Here, I will try to ELI5 those concepts and abstraction for the AArch64 platforms. However, the idea is basically the same for any other. Throught this writing, I will assume that you are familiar with stuff like compilers, linkers, ELF format, ISA and basic assembly code. Feel free to stop reading at any point and gain more insight on those topics as they will help broaden your knowledge on system-level programming & computer hardware;)

Calling Conventions
===================

Everyone writes programs and codes in their own way. The compiler they choose to use can also be different; so does the hardware they worked on; or even the programming language itself. All these (not limited to) factors can affect the final machine code of the program.

insert different compilers /w same code producing diff machine code here

Naturally, we expect our program to behave the same with the same [high-level] code; doesn't matter the compiler or the underlying hardware. If I wrote a static/dynamic library for others to use (e.g., libssl, libcurl) I expect it to work for the platform I compiled it to, whether AMD64 or AArch64. The kernel/OS version, CPU generation (e.g. Apple M1 vs M4) or the compiler version should NOT matter. How is this achieved?

I mean really think about it: How does a program/library you download from the internet works on your machine? The CPU you have might not have been around when the program was first compiled!

A function can be defined as a block of code with a well-defined interface and behavior that can be called multiple times. The program/code that calls the function is called caller, and the function itself is called callee. The interface of a function is where we will be focusing on.

On higher-level languages the interface of a function is sometimes called an API (Application Programming Interface). Programmers must know this interface to make a function call. The order of the parameters, and their type matters for languages like C, Rust, Java. However, languages like Python and Ruby support named parameters, where the order doesn't "really matter".

insert API example here

APIs are higher-level interfaces that is used on higher-level languages. They have language-specific parameters and types. So, they are generally intended to be used for the language they are written in. But in the end they are all compiled into machine code and programs talk with each other with that language; not C or Swift.

We have seen that even if the language might be the same, the compiled machine code can be different. And if the machine code is different: how would the APIs work? Below is an example function that's compiled using two different compilers /w different machine code. Yet, they work. How?

insert a C function and a program compiled with different compilers here

Notice how even the machine code is generally different, some stuff is the same. Especially the begging and the end. And it's not just mere coincidence. The code you see there is called ABI (Application Binary Interface). They define how functions (or subroutines) should be called and returned from at the machine code level. You can compare them to APIs; the idea is the same, but the language/level they work on is different.

insert basic API vs ABI here

Just like how the design of an API is unique to the author of the software, the design of an ABI is also unique to the it's platform. There are many design factors that defines an ABI (e.g., calling conventions, data type & alignment). The one we will focus on is Procedure Call Standard (PCS) for AArch64 platforms. Also known as - calling convention for AArch64.

> I won't be explaining the data types & alignment as to keep the writing on topic: calling conventions. They deserve their own writing, which I might do on another time;)

insert different standards and machine abstractions here

A Calling Convention is a scheme that defines how functions receive their parameters and return their results. They specify how a program should handle functions at the machine code level. Below are some of the schemes that they define (not limited to):
* How parameters are passed & returned
* Which registers the callee must preserve
* How the stack should be managed between the callee and caller

> From now on I will use the ABI and calling convention interchangeably to keep things on ELI5 level. But know that they are NOT the same time. Calling conventions are just a subset of ABI design.

Each platform has their own conventions, but the sane ones usually share a lot of similarities. Below are the preferred calling conventions for each widely used platforms.
* AMD64 (x86_64): Sytem V ABI
* AArch64 (ARM64): Procedure Call Standard for AArch64
* RISC-V: RISC-V Calling Convention
* PowerPC: System V ABI for PowerPC

The one we are focusing on is AArch64. But before we move on to that, let's talk about registers and stack as they are the blocks which a calling convention is build upon.

Machine Registers
=================

TODO.

AAPCS64
=======

TODO.