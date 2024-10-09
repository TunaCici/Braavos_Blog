---
title: "seL4 Microkernel: Architecture"
tags:
- sel4
- microkernel
- architecture
date: 2024-10-09T00:00:00Z
header_image_alt: "TODO"
---

The balance between safety and security versus high-performance and high-throughput has always been a challenge in OS design. Modern hardware allows separating trusted software from normal ones (e.g., user/kernel space, EL). The trade-off is the communication cost. It requires context switches, which are undesirable in high-performance systems.

This trade-off led to monolithic kernels, where high-performance software (e.g., drivers) resides in the trusted world. However, this increases safety and security risks. If the software fails, the entire system goes with it. Microkernels, on the other hand, minimizes the software in the trusted world, only including essential software like the scheduler and MMU configuration. This enhances system safety as small, verifiable code is easier to validate. However, performance may suffer.

[monolithic-vs-micro.png]

seL4 is a secure, formally verified microkernel operating system with fine-grained access control and support for virtual machines. It is the world's fastest microkernel, balancing both security and performance. How this balance is achieved is a feat of software system design and engineering.
In this series I will be going over the mechanisms that allow safe, secure and high-performance systems to be built with seL4 at the center. Refer to the white paper for a detailed understanding of the project's background, objectives, and real-world applications.

Abstractions
============

[abstractions.png]
The seL4 acts as a hardware abstraction layer for the software running in the normal world. These abstractions are backed by well-designed, secure, yet simple mechanisms. You are probably already familiar with some of them.
- Capabilities
- Threads
- Inter-process Communication
- Untyped
- Interrupts
- Faults

Each abstraction and their mechanisms will be explored in the next parts.

Root Task
=========

seL4 follows a "no-policy" design approach by providing only the mechanisms for enforcing security and access control, but it doesn't impose specific policies on their use. The responsibility, however, must be given to someone and that is the root task (a.k.a. init task).

[root-task.png]
After the kernel boots, an initial thread is created and all system resources (e.g., untyped memory, interrupt control) are given to it in a well-defined way. It is then the responsibility of this task to set up the normal world such as drivers, filesystems and system services.

Since seL4 lacks storage drivers and filesystems, loading the root task from a disk is impossible. To resolve this, both the seL4 kernel and the root task are included in the boot image. The kernel then loads the root task from a predefined memory location.

Kernel Objects
==============

TODO.

System Calls
============

TODO.

Object Methods
==============

TODO.

Example System #1–3
===================

TODO.
