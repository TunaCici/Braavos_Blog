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

There are many registers defined in AArch64 and the availability of them depends on the architecture version (e.g., ARMv8 vs. ARMv9) and the platform vendor. However, all of them are at least expected to implement the basic register banks: general-purpose and (optionally) SIMD. We will focus on general-purpose registers and what their purposes.

insert AArch64 general-purpose related image here

AArch64 provides 31 general-purpose registers, each 64 bits wide. They are named X0 … X30. Although the general-purpose registers are equal and interchangeable at the architecture level, in practice their purpose is defined by the AAPCS64 and most sane compilers/programmers adhere to it.

> The following sections are taken from Maria Azeria Markstedter's ARM Assembly: Internals & Reverse Engineering Book. Shout out to her for the great book!

X0-X7
=====

These registers are used for argument registers to pass parameters and return a result. More on this later.

X8
===

Pointer to where to write the return value if >128 bits, otherwise scratch register. It can be used to pass the address location of an indirect result.

X9–15
=====

These are caller-saved temporary registers used to preserve values across a call to another function. The affected registers are saved in the stack frame of the caller function, allowing the callee function to modify these registers.

X16-X17
=======

These are intraprocedure-call temporary registers that can be used as temporary registers for immediate values between function calls. They can be used for ordinary computations within any given function. However, AAPCS64 gives them a different purpose.

For example, if a program calls a function defined in a shared library such as malloc(), this function call may be implemented via a call through the procedure linkage table (PLT) to call the malloc() implementation inside another module.

The PLT stub responsible for find and transferring execution to the malloc() function in the other library can use the X16 and X17 registers as intraprocedural call registers freely, without having to take care not to corrupt their values. LLVM, for example, will compile PLT stubs that make use of X16 and X17. 

> Also see GCC's veneer for more examples on X16 and X17 usage.

insert X16 and X17 example usage here

X18 (Platform Register)
=======================

The X18 platform is a general-purpose register like any. The AAPCS, however, reserves it to be the platform register, pointing to some platform-specific data. More on this later.

X19-X28
=======

These are callee-saved registers that are saved in the stack frame of the called function, allowing the function to modify these registers but also requiring it to restore them before returning to the caller.

X29 (FP)
========

It is used as a frame pointer (FP) to keep track of the stack frame. More on this later.

X30 (LR)
========

It is the link register (LR) holding the return address of the function. More on this later.

insert table of general-purpose registers here

SIMD and Floating-Point Registers
=================================

In addition to the 64-bit general-purpose registers, AArch64 also supplies a series of 32 x 128-bit vector registers for use in optimized single instruction multiple data (SIMD) operations for performing floating-point arithmetic. These registers are each 128-bits long and named V0 … V31.

Similar to general-purpose registers, V0 … V7 the  first eight SIMD registers are used for argument registers to pass parameters and return a result. The rest is either used as scratch registers. However, there are some calling convention details. More on this later.

insert SIMD registers for AArch64 here

AAPCS64
=======

Most platform's author/owner describes their calling conventions for public use. After all that's what they are meant for: for everyone to use and adapt. We are currently only interested in Arm's.

Arm® Architecture describes their calling conventions on their GitHub page named Procedure Call Standard for the Arm® 64-bit Architecture (AArch64). We will shortly call it: AAPCS64. 

There are other documents on that Arm's GitHub page that further describes their ABI. Check them out if you are curious!

insert Arm ABI documents and their purpose for 64-bit here

Inside the AAPCS64 there are multiple sections and each deserve their own writings. We are only going to talk about calling conventions, but for the curious: here's other sections that's described in it:

* Data Type and Alignment
* The Base Procedure Call Standard
* Standard Variations
* C and C++ Language Mappings

AAPCS64 is kind of a long and it would be pointless to explain everything in it. I will only talk about the parts that I consider to be the most useful when doing casual programming for AArch64 platforms.

Parameter Passing
=================

Naturally, [on any language] when calling a function the caller and the callee must agree on how the parameters must be passed around on machine level. AAPCS64 defines this in Section 6. The Base Procedure Call Standard. It describes that:

* X0 … X7: Should be used for the first eight (8) parameters
* Stack: Should be used for any additional parameters

I think it is pretty straightforward. We can even see this in action. Let's look at an example C function with 4, 8 and 12 parameters.

insert C function with 4, 8 and 12 parameters and it's machine code here

As you can see the first (1st) parameter is stored in X0, second (2nd) in X1, third (3rd) in X2 and so on… After the eight (8th) the additional parameters are stored in thread stack. This is where things get interesting.

Stating the obvious: CPU registers are fast and stack memory is slow. We want to stay away from accessing the memory as much as possible. Generally, compilers are pretty good at optimizing on this and you won't have to worry about the amount of parameter a function takes.

Sometime, however, you might need those extra nano/microseconds (e.g., low-latency systems).

Result Return
=============

This one, again, is pretty straightforward and clear. AAPCS64 states that the function return value should be stored in:

* X0 

You can see from the below C function that the uint64_t return value is stored in X0 before the call to RET (function return).

insert C functions that returns the value in X0 here

> Personal note: I always wondered why C/C++ functions always returned one (1) value. What if I want to return two values like 2D coordinates of {x, y}? Well, now I know: the ABI just doesn't support it. Probably for good reasons. 

> Obviously, I learned about objects/structs and then used them to "return more than one (1) value". But still, at the machine code level, a function still returns one (1) value: reference/pointer of an object.

The Stack
=========

Almost all modern languages use the concept of stack. They are used for various reasons. I would like to give examples but am no language designer;( One thing I know is that they are used when calling functions.

The next parts assume that any language is designed to make use of the stack. There are, however, languages that DON'T have stack. See Parrot or other functional languages.

Each thread in a program has its own stack. According to AAPCS64, a stack is a continuous memory region that the thread can use for storage of local variables and for passing additional arguments [if the first eight (8) register are insufficient].

insert stack representation in a C program here

The concept of stack is a pretty big topic and [again] deserves it's own writing. From now on I will assume that you have some familiarity with the thread stack in C.

In AAPCS64, a stack is defined to have the following three (3) values:

* Base: base of the stack (e.g., 0x800)
* Limit: limit of the stack (e.g., 0x400)
* Current Extent: current tack pointer (SP) (e.g., 0x600)

The SP is basically a pointer that moves from base to limit as the stack grows. And from limit to base as the stack shrinks. Below is a visualization on this concept.

insert basic AAPCS64 stack visualization here

AAPCS64 further defines the stack to be full-descending. Now, there are four (4) different stack organizations. I am not going to explain them here as there is a great note about them by John Winans. I highly recommend you to read at is it's very informative and pretty short. It shouldn't take more than 15 minutes to understand it.

insert full-descending stack visualization here

Last, but not least, the AAPCS64 uses the stack to passing parameters. If there are more than eight (8) parameters, that is the X0 … X7 is already used, then the rest is put in stack. Below is an example C function and it's machine code that shows how stack is used to pass parameters.

insert C function that uses stack to pass parameters here

Machine Registers
=================

Like we have seen before, Arm 64-bit architecture defines two mandatory register banks: general-purpose and SIMD. Technically, you can use them however you want, but in practice each have their own purposes defines by AAPCS64.

Let's talk about each of those registers.

X0 … X7: These register are used to pass the first eight (8) parameters into a function. The caller fills these register and then the callee function can use them however it sees fit. 

Before the function returns, the X0 must be updated by the callee to store the function return value. However, if the return type is void or not used, compilers can optimize this away.

insert optimized X0 return value register here

X8: Also known as indirect result register. Frankly, I don't think the usage of this is not very well defined. AAPCS64 states that "This is used to pass the address location of an indirect result, for example, where a function returns a large structure." From what I gather around the internet, each OS (e.g., Windows) and language (e.g., C++) uses it a bit differently.

Basically, if an object is too big to fit into a 64-bit register (or divided into multiple ones) the caller reserves a memory region (via stack or heap) and then passes a pointer/reference to it inside the X8 register. After that, the callee is able to access to that [big] object. Below is a basic example on this.

insert big ahh C structure that's passed onto a function here

X9 … X15: These are just scratch registers. Meaning a function can use them freely as temporary storages for intermediate calculations or data. AAPCS64, however, gives them a purpose and a name: caller-saved registers.

These register should be used by a function for values that are NOT needed after the next function call (if exists). Now, when I first saw this it seemed so vague. Like, okay… How do I know which values are needed and which are not? Why can't I just use them normally?

insert highlighted caller-saved registers in CPU here

For that I needed an example. I couldn't find a good one. But I did find one explanation that made it all make sense. You can read it If you want, but if you can wait a little bit, I prepared an example after this section that will help you understand on why they exists and why you shouldn't just use them normally.

X16 - X17: Ah the twin registers, also known as IP0 and IP1. They are the intra-procedure-call temporary registers. Like others, they can be as scratch registers, but AAPCS64 marks them as "corruptible by function". Meaning, after ANY function call, the caller assumes them to be corrupt and act accordingly.

insert arm BL instruction here

In practice, however, they are used by veneers and other similar pieces of code. In Arm A64 ISA, the BL branch line instruction can only in the range of PC +/-128MiB. Most of the time your code won't have to jump around that much, but sometimes it happens (e.g., dynamic libraries OR switching from lower-address to higher-address range in kernel syscalls). To overcome this range limitation, compilers (like GCC) uses a small piece of code called veneers. I don't really looked at how they work, but know that they used to jump between codes that are FURTHER away from each other in [virtual] memory. Hence the IP0 and IP1 are called the intra-procedure-call registers.

insert veneer code generated by GCC here

X18: It's called the platform register. Like the name suggests, each platform (e.g., Linux, Windows, macOS) can define whatever use for it they want. The exact usage of it can also change from platform version to version.

* Apple (macOS, iOS, iPadOS…): Reserved
* Linux: Task Structure pointer in kernel mode; (?) in user mode
* Windows: Thread Environment Block pointer in user mode; reserved in kernel mode

insert Apple XNU code block that reserves the x18 register here

As you can see, it isn't much used or have a well-defined usage on some common platforms. It's recommended (at least for now) just avoid using it.

X19 .. X28: Just like the previous scratch registers (caller-saved ones), these registers are also scratch registers. Similarly, AAPCS64 gives them their own purpose and name: callee-saved registers.

These register should be by the function if they ARE needed to be preserved after the call. The callee is the one responsible of saving AND restoring them.

insert highlighted callee-saved register in CPU here

Just like the caller-saved registers, the explanation by Arm and AAPCS64 seems confusing. In the coming section I will give a concrete example that will help you understand what it's meant by callee & caller saved registers.

X29: This is the register that holds the frame pointer as describe by AAPCS64. A frame pointer is.. well a pointer that is used to keep track of the stack frame. When a new stack frame is created (after a function call) the base address of it is stored inside this register.

insert frame pointer diagram and relation to stack here

In practice, compilers can generate machine code that makes use of this register to store the frame pointer and then use it to locate certain variables/object that resides within the stack. They can technically just use the SP stack pointer, but that pointer is dynamic! It moves during the runtime so it's harder (maybe impossible?) to use it to access local variables. Frame pointers, however, are static and do not change during a function's lifetime. So, they can be easily used to calculate variable locations.

X30: Last, but not least, the link register! Have you ever wondered when a function is called how does it know where to return after? Well, the link register is how.
Before a thread executes the BL branch with link instruction (basically calling a function) the link register is set to the current PC program counter so that when the function returns with RET this register would be pointing to the caller function. The exact value hold in it is the address of the NEXT instruction right after BL.

insert link register example here

The link register only holds the one-previous function's address. So, what about nested function calls?
AAPCS64 further details that before calling a function and modifying the link register, its current value should be pushed to the stack. And then, when the called function returns, the value is popped and written back to the link register. Therefore, preserving the order of function calls!

> One last thing I want to talk shortly about is SIMD registers. AAPCS64 also talks about them, but not in great detail compared to general-purpose ones as their purpose is pretty straightforward.

> SIMD registers are named V0 … V31. The first eight (8) register V0 … V7 are used to pass parameters and to return the result value from a function. Registers V8 … V17 are callee-preserved registers that have seen before. The rest can be used as a scratch register like normal.

Caller-Saved & Callee-Saved
===========================

Now this is a new term we just saw: caller-saved registers. They are general-purpose registers that are assumed by the callee to be "clobbered" after a function call. To understand what this means let's look at an example in both the callers and callees perspectives.

Example: Let's say you have a for loop. The loop does some calculations and then calls for a function. Inside the function, even more calculations are done before returning. Our goal is to optimize the usage of scratch register. We want to use them and keep away from the stack memory as much as possible (the less str / ldr used the better).

insert register vs stack here

TODO.