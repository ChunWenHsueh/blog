---
title: "Ch2 OS Structure"
date: 2025-03-06T00:00:00-07:00
draft: false
showToc: true
TocOpen: false
categories: [Operating System]
---

# OS Structure

## OS Services
- User interface
- Program Execution
- I/O operations
- File-system manipulation
- Communication (not just between computer, but also between cores, processes, etc)
- Error detection
- Resource allocation
- Accounting
- Protection and security

### User Interface
CLI (Command Line Interface)  
Shell: Command-line interpreter (ZSH, BASH), adjusted according to user behavior and preferences (this is also the reason why we need shell even if we have GUI)

GUI (Graphical User Interface)  
Icon represent files, programs, actions, etc

### Communication
message passing  
passing data in the memory from a to b, a->kernel->b instead of a->b since we need protection

shared memory  
use system call to create a shared memory segment, both a and b can see the data in the shared memory


## OS Application Interface
- System calls
- API

### System Calls
Request OS services
- Process control
- File manipulation
- Device management
- Information maintenance
- Communications

System call is made via a software interrupt to kernel.  
Generally available in assembly language.

### API
Users mostly program against API instead of system call.  
Commonly implemented by C.

Three common APIs:
- Win32 API (Windows): API for Windows
- POSIX API (Unix/Linux/Mac OS): Portable Operating System Interface for Unix
- Java API (Java): API for Java virtual machine

Why use API?
- Simplicity: API is designed for applications
- Portability: API is an unified defined interface
- Efficiency: Not all functions require OS services or involve kernel

Three general methods are used to pass parameters between a running progarm and the OS:
- Pass parameters in registers
- Store parameters in a block or table in memory, and pass the address of the block as a parameter in a register
- Push (or pop) the parameters onto the stack

## OS Structure
- Simple OS Architecture
- Layer OS Architecture
- Microkernel OS
- Modular OS Structure
- Virtual Machine
- Java Virtual Machine

User goals: operating system should be convenient, easy to use, reliable, safe, and fast.

System goals: operating system should be easy to design, implement, and maintain; flexible, reliable, error-free, and efficient.

### Simple OS Architecture
Only one or two levels of code.  
Drawback: un-safe, difficult to enhance

### Layer OS Architecture
Lower levels independent of higher levels. Nth layer can only access lower n-1 layer.  
Easier debugging and maintenance.  
Less efficient, difficult to define layers.

```Text
User Program (higher level)
+----+
I/O Management
+------+
Device Driver
+--------+
Memory Management
+----------+
Process Allocation multiprogramming
+------------+
Hardware (lower level)
+--------------+
```

### Microkernel OS
- Kernel be as small as possible
- Kernel is for message passing
- Modulize the OS components and move them to user space
- Easier for extending and porting

### Modular OS Structure
Most modern OS implement kernel modules
- Uses object-oriented approach
- Each core component is separate
- Each talks to the others over known interfaces
- Each is loadable as needed within the kernel

### Virtual Machine
- Treats hardware and OS as they were all hardware
- virtual machine provides an interface identical to the underlying bare hardware
- Difficult to achieve due to "critical instruction": some instruction will behave differently in user mode and kernel mode

```Text
Traditional System                 Virtual Machine System
--------------             ---------------------------------------
|  processes |             |  processes |  processes |  processes |
|------------|             |------------|------------|------------|
|   kernel   |             |  kernel    |  kernel    |  kernel    |
|------------|             |------------|------------|------------|
|  hardware  |             |  VM1       |  VM2       |  VM3       |
|            |             |--------------------------------------|
|            |             |  virtual-machine implementation      |
|            |             |--------------------------------------|
|            |             |  hardware                            |
--------------             ----------------------------------------
```

Some hardware supports vm mode, can let vm run privilage instructions and critical instructions in user mode 

- VM provides complete protection of system resources
- solve system compatibility problems, i.e. run old OS on new hardware
- increase resources utilization, for example: cloud computing

Vmware (Full Virtualization)
- Run in user mode as an application on top of OS
- Virtual machine believe they are running on bare hardware but in fact are running inside a user-level application

Para-virtualization: Xen
- master OS (Xen) and guest OS (Linux, Windows, etc)
- Guest OS is modified to run on Xen

### Java Virtual Machine
- Compiled Java programs are platform-neutral bytecodes executed by the Java Virtual Machine (JVM)  
- Just-In-Time (JIT) compilers increase performance

```Text
+---------------------------+
| Java program .class files |----+   +----------------------+
+---------------------------+    |   | +------------------+ |
                                 +-->| |   class loader   | |
+---------------------------+    |   | +------------------+ |
|   Java API .class files   |----+   |           |          |
+---------------------------+        |           v          |
                                     | +------------------+ |
                                     | | Java interpreter | |
                                     | +------------------+ |
                                     +----------------------+
                                                 |
                                                 v
                                    +-----------------------+
                                    |     host system       |
                                    | (Windows, Linux, etc.)|
                                    +-----------------------+
```