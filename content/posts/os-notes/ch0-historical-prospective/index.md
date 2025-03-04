---
title: "Ch0 Historical Prospective"
date: 2025-03-02T00:00:00-07:00
draft: false
showToc: false
TocOpen: false
categories: [OS]
---

# Historical Prospective

## Mainframe: Batch Systems
  - One job at a time
  - No interaction between users and jobs
  - CPU is often idle (I/O speed << CPU speed)
  - OS doesn't need to make any decision

## Mainframe: Multi-programming System
  - Overlaps the I/O and computation of jobs
  - Spooling (Simultaneous Peripheral Operation On-Line)
    - I/O is done with no CPU intervention
    - CPU just needs to be notified when I/O is done
  - Memory management, CPU scheduing, I/O system comes in

## Mainframe: Time-sharing System (Multi-tasking System)
  - interactive system
    - CPU swiches among jobs so frequently that users may interact with programs
  - Switch job when
    - finish
    - waiting I/On
    - a short period of time
  - Users will feel like the task run at the same time

  New features appear to address new problems
  - virtual memory
  - file system, disk management
  - process synchronization, deadlock

## Desktop Systems: Personal Computers
  - GUI
  - I/O devices
  - Lack of file and OS protection

## Desktop Systems: Parallel Systems
  - multiprocessor or tightly coupled system
  - More than one CPU
  - communicate through shared memory

Parallel systems can be further classified into
- Symmetric multiprocessor system (SMP)
    - Each processor runs the same OS
    - Most popular multiprocessor architecture
    - Require extensive synchronization to protect
- Asymmetric multiprocessor system
    - Each processor is assigned a specific task
    - One master CPU and multiple salve CPUs
    - Mmore common in extremely large systems

Multi-Core Processor
- A CPU with multiple cores on the same die (chip) 
- On-chip communication is faster than between-chip communication
- Also use less power than multiple single-core chips

## Memory Access Architecture
- Uniform Memory Access (UMA)
  - Most commonly used todday with SMP
  - identical processors
  - Equal access times to memory
- Non-Uniform Memory Access (NUMA)
  - Each CPU has it's own memory and can also visit other CPU's memory
  - Often made by physically linking two or more SMPs
  - Memory access across link in slower
  - Example: IBM Blade server

## Distributed Systems
  - Each processor has its own local memory
  - communicate with various communication lines (I/O, network)
  - easy to scale
  - Pruposes: resource sharing, load sharing, reliability
  - Client-Server Distributed Systems
    - easier to manage and control resourses
    - server becomes the bottle neck
    - Example: FTP
  - Peer-to-Peer Distributed System
    - decentralized system
    - Example: torrent, internet
  - Clustered System
    - share storage and linked via local area network (LAN)

## Special-purpose Systems
- Real-Time Operating Systems
- Multimedia Systems
- Handheld Systems

### Real-Time Operatinig Systems
- fixed-time constraints, keeping deadlines
- guaranteed response and reaction times
- Soft real-time
  - Missing deadline is acceptable
  - Examples: multimedia streaming
- Hard real-time
  - Missing deadline results in a fundamental failure
  - Secondary storage (disk) limited or absent

### Multimedia Systems
- On-demand/live streaming
- Compression

### Handheld Systems
- smart phones
- specialized OS