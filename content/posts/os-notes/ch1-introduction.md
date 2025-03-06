---
title: "Ch1 Introduction"
date: 2025-03-04T00:00:00-07:00
draft: false
showToc: true
TocOpen: false
categories: [Operating System]
---

# Introduction

## What is an Operating System?

### computer system has four components:
- Hardware
- Operating System
- Application programs
- Users

OS controls and coordinates the use of hardware/resources. Provides Application Programming Interface (API) to users.

``` Text
User program      Executable binary       user
    |                     ^
    v                     |
  Compiler   ----->    Linker             compiler
                          ^
  user mode               |
------------        System library        OS interface
  kernel                  ^
                          |
                          v
                  Operating System        OS
                   Device drivers
                          ^
                          |
                          v
                     Architecture         Hardware
```

### Definition of an OS:
- Resource allocator: manages and allocates resources to insure efficiency and fairness
- Control program: controls the execution of user programs and operation of I/O devices
- Kernel: the one program running at all times (all else begin either system or user programs)

Kernel is the most used term for OS. Kernal == OS.

### Goals of an OS:
- Convenience: make the computer more convenient to use
- Efficiency: use conputer hardware in an efficient manner

Two goals are contradictory. OS must make trade-offs between them.

### Importance of OS:
- System API are the only interface between user programs and hardware, API are designed for general-purpose, not performance driven
- OS code cannot allow any bug
- The owner of OS technology controls the software and hardware industry
- OS and computer architecture influence each other

### Modern Operating Systems:
- x86 platform
  - Linux
  - Windows
- PowerPC platform
  - Mac OS
- Smartphone
  - Android
  - iOS
- Embedded OS
  - Embedded Linux
  - Raspberry Pi, Xbox, etc

## Computer System Organization
Goal: Concurrent execution of CPUs and devices competing for memory cycles

### An exmaple of how I/O works:

```Text
         Bus                          I/O
 CPU  <---+----> Device controller <-------> Device
          |     (Status Registers
          |      Date Registers
          |      Buffer)
Memory <--+
```
Each device controller is in charge of a particular device type. Each controller has a local buffer storage. Since I/O is much slower than CPU, if we don't use buffer, CPU will be idle while waiting for I/O.  
I/O is from the device to controller's local buffer.  
CPU moves data from/to main memory to/from the local buffer.

Busy/wait output  
CPU waits for I/O to complete. CPU is idle (checking whether device is ready) while waiting.

Interrupt-Driven I/O  
Busy/wait is very inefficient. Interrupts allow a device to change the flow of control in the CPU.

```Text
    CPU               Controller

device driver        
initiates I/O -------------+
    |                      v
    |                 initiates I/O
    |                             |
CPU executing checks for          |
interrupts between instructions   |
                                  v
                      input ready, output complete or error
    +---------------  Generates interrupt signal
    v
CPU receiving interrupt,
transfers control to interrupt handler
    |
    v
interrupt handler processes data
returns from interrupt
    |
    v
CPU resumes processing of interrupted task
```

Modern OS use interrupt-driven I/O.  
Hardware interrputs are called signal.  
Software interrupts are called trap.

### Commom Function of Interrupts:
- Interrupt transfer control to the interrupt service routine, through the interrupt vector, which contains the addresses (fuction pointer) of all service (i.e. interrupt handler) routines
- Interrupt architecture must save the address of the interrupted instruction
- Incoming interrupts are disabled while another interrupt is being processed to prevent a lost interrupt

### Storage-Device Hierarchy  
- Storage systems organized in hierarchy. Speed, Cost, Volatility (will data lost when power is off)
- Main memory, only large storage media that the CPU can address directly
- Secondary storage, extension of main memory that provides large non-volatile storage

### Caching  
Information in use copied from slower to faster storage temporarily. If data is not in cache, it is copied to cache from main memory. If data is not in main memory, it is copied to main memory from secondary storage.

### Coherency and Consistency Issue  
Issue: Change the copy in register make it inconsistent with other copies.  
- Single task accessing: No problem, always use the Hightest level copy
- Multi-task accessing, Distributed system: hard to maintain consistency

## HW protection

Protect is not security. Protect means to ensure that an incorrect program cannot cause other programs to execute incorrectly. How do CPU knows the instruction is from OS or user program?

### Dual-Mode Operation
Provide hardware support to differentiate between at least two modes of operation:
- User mode: execution done on behalf of a user
- Kernel mode (or Monitor mode, System mode): execution done on behalf of the operating system

Mode bit added to computer hardware to indicate the current mode: kernel (0) or user (1).  
When an interrupt occurs, hardware switches to kernel mode.  
When the CPU is executing in kernel mode, it can execute privileged instructions.

All I/O instructions are privileged instructions.  
Must make sure that user programs could never gain control of the computer in kernel mode. (i.e. a user
program that, as part of its execution, stores a new
address in the interrupt vector)

### Memory Protection
HW support: two registers for legal address
- Base register: holds the smallest legal physical memory address
- Limit register: contains the size of the range

Memory outside the range is protected.

### CPU Protection
Prevent user program from not returning control.  
For exampel, when we run `while (1)`, we still have control of the OS.

HW support: Timer, interrupts computer after specified period of time.
- Timer is decremented every clock tick
- When timer reaches zero, an interrupt occurs

Timer commonly used to implement time sharing  
Load-timer is a privileged instruction
