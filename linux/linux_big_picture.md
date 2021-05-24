---
title: The Big Picture
description: Linux の概要についてのメモ
date: 2021-02-02
tags: ["Linux"]
---


The most effective way to understand how an operating system works is through **abstraction**.  
Abstraction enable us to ignore most of the details.

## Levels and Layers of Abstraction in a Linux System

We arrange components into **layers or levels**.

A linux system has **three** main levels.

```
-----------------------------
User Process
	+ Graphical User Interface
	+ Servers
	+ Shell
-----------------------------
Linux Kernel
	+ System Calls
	+ Process Management
	+ Memory Management
	+ Device Drivers
-----------------------------
Hardware
	+ Processor (CPU)
	+ Main Memory (RAM)
	+ Disks
	+ Network Ports
-----------------------------
```

#### Kernel
**kernel** is the core of the operating system.  
The kernel is software residing in memory that tells the CPU waht to do.  
The kernel manages the hardware and acts primarily as an **interface** between the hardware and any running program.  

#### Process
Process: the running promgrams that kernel manages  


#### Kernel Mode and User Mode
* The kernel runs in **kernel mode**
* The user processes run in **user mode**

Code running in kernel mode has **unrestricted** access to the processor an main memory.  
-> powerful but **dagerous** privilege that allows a kernel process to easily crash the entire system  
The area that only the kernel can access is called **kernel space**.

User mode **restricts** access to a subset of memory and safe CPU operations.  
**User space** refers to the parts of main memory thta the user processes can access.  
If a process makes a mistake and crashes, the consequences are **limited** and can be cleaned up by the kernel.  

## Hardware: Understanding Main Memory

**main memory** is perhaps the most important.  
This is where the running **kernel and processes** reside.  
All input and output from **peripheral devices** flows through main memory.  
A CPU is just an operator on memory; it reads its instructions and data from the memory and writes data back out to the memory.


## The Kernel
Nearly everything that the kernel does revolves around main memory.  
One of the kernel's tasks is to **split memory** into many subdivisions, and it must **maintain** certain state information about those subdivisions at all times.

* Process: determine which processes are **allowed** to use the CPU
* Memory: keep **track** of all memory
* Device drivers: operate **hardware** for processes. acts as an interface between hardware and processes
* System calls and support: processes normally use system calls to **communicate** with the kernel

#### Process Management
Process management describes the starting, pausing, resuming, and terminating of processes.  

On amy mordern operating system, many processes run "simultaneously." The processes behind these applications typically do **not** run at **exactly** the same time. 

e.g. A system with a one-core CPU
-> Many processes may be **able** to use the CPU, but only one process may actually use the CPU at any given time.  
-> Each process uses the CPU for another small fraction of a second (**time slice**), then pauses.

The act of one process giving up control of the CPU to another process is called a **context switch**. The kernel is responsible for context switching.

```
Kernel Process Management

# Interrupt preceding time
1. The CPU (the actual hardware) interrupts the current process based on an internal timer, switches into kernel mode, and hands control back to the kernel.

2. The kernel records the current state of the CPU and memory, which will be essential to resuming the process that was just interrupted.


3. The kernel performs any tasks that might have come up during the preceding time slice (such as collecting data from input and output, I/O, operations).

4. The kernel is now ready to let another process run. The kernel analyzes the list of processes that are ready to run and chooses one.

5. The kernel prepares the memory for this new process, and then prepares the CPU.

6. The kernel tells the CPU how long the time slice ofr the new process will last.

7. The kernel switches the CPU into user mode and hands control of the CPU to the process.
```

The kernel runs **between** process time slices during a context switch.

#### Memory Management
Because the kernel must **manage memory** during a context switch, it has a complex job of memory management.  

Conditions of memory management
* The kernel must have its own **private area** in memory that user processes can't access.
* User processes can share memory
* Some memory in user processes can be read-only
* The system can use more memory than is physically present by using disk space as auxiliary.

Now, modern CPUs include a **memory management unit** (MMU) that enables a memory access scheme called **virtual momemory**. 

When using virtual memory, a process does not directly access the memory by its ***physical*** location in the hardware. Instead, the kernel sets up each process to act as if it had an ***entire*** machine to itself. When the process accesses some of its memory, the MMU intercepts the access and uses a memory address map to translate the memory location from the process into an actual physical memory location on the machine. 

#### Device Drivers and Management
A device is typically accessible only in kernel mode because **improper** access (such as a user process asking to turn off the power) could crash the machine.  
Another problem is that different devices rarely have the same **programming interface**, even if the devices do the same thing. Therefore, device drivers have traditionally been part of the kernel, and they strive to present a uniform interface to user processes.

#### System Calls and Support

**System Calls** (or **syscalls**) perform specific tasks that a user process alone cannot do well or at all. For example, the acts of opening, reading, and writing files all involve system calls.

Two syscalls, `fork()` and `exec()`, are important to understanding how processes start up:
* `fork()`: the kernel creates a nearly identical copy of the process.
* `exec(program)`: the kernel starts `program`, replacing the current process.

Other than init, **all** user processes on a Linux system start as a result of `fork()`, and most of the time, you also run `exec()` to start a new program instead of running a copy of an existing process.

Example: executing `ls` command
```
shell --> fork() --> shell ----------- wait --------------> shell
                 --> coply of shell --> exec(ls) --> ls -->
```

`fork()` in order to prepare for failure of `exec()`.


#### User Space
Most of the real action on a Linux system happens in user space. User space has **layers**, from user interface to system components (which is close to kernel).  



