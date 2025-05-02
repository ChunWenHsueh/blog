---
title: "Ch3 Processes Concept"
date: 2025-03-09T00:00:00-07:00
draft: false
showToc: true
TocOpen: false
categories: [Operating System]
---

# Processes Concept
- Process Concept
- Process Scheduling
- Operations on Processes
- Interprocess Communication

## Process Concept
Program: passive entity stored on disk (executable file)
Process: active entity, program in execution in memory

A process includes:
- Code segment (text section)
- Data section: global variables
- Stack: temporary local variables and functions
- Heap: dynamically allocated memory
- current activity: program counter, register contents
- A set of associated resources

### Threads
- lightweight process, basic unit of CPU utilization
- All threads belonging to the same process share the same code, data section, OS resources
- But each threads has its own thread ID, program counter, register set, stack

### Process State
- New: process is being created
- Ready: the process is in the memory waiting to be assigned to a  processor
- Running: instructions are being executed by CPU, might be interrupt and switch to Ready state
- Waiting: process is waiting for some event to occur (I/O completion, etc)
- Terminated: process has finished execution

### Process Control Block (PCB)  
Info about each process
- Process state
- Program counter
- CPU registers
- other information (priority, memory limits, list of open files, etc)

### Context Switch  
- Kernel saves the state of the old
process and loads the saved state for the new process
- Context switch time is pure overhead, the system does no useful work while switching
- Switch time depends on
  - memory speed
  - number of registers
  - existence of special instructions, e.g. a single instruction to save/load all registers
  - hardware support, multiple sets of registers

```Text
+-------------+----------------------+--------------+
|  process P0 |   operating system   |  process P1  |
+-------------+----------------------+--------------+
       |                                    │       
       v                                    │       
   executing                                │       
       |                                    │       
       +-----------> interrupt or           v       
                     system call          idle      
       │                  │                 │       
       │                  v                 │       
       │         save state into PCB0       │       
       v                  │                 │       
     idle                 v                 │       
       │                 ...                │       
       │                  │                 │       
       │                  v                 v       
       │        reload state from PCB1─>executing   
       │                                    │       
       v                                    v       
```

## Process Scheduling
- Multiprogramming: CPU runs process at all times to maximize CPU utilization
- Time sharing: switch CPU frequently such that users can interact with each program while it is running
- Processes will have to wait until the CPU is free and can be re-scheduled

### Process Scheduling Queues
- Job queue (New State): all processes in the system
- Ready queue (Ready State): processes that are in main memory and ready to run
- Device queues (Wait State): processes that are waiting for I/O devices

```Text
+-----------+                                                    +---+
|ready queue|--------------------------------------------------->|CPU|
+-----------+                                                    +---+
      ^                                                            |  
      |           +-----+   +------------+   +-------------+       |  
      +-----------| I/O |<--|  I/O queue |<--| I/O request |<------+  
      |           +-----+   +------------+   +-------------+       |  
      |                                                            |  
      |                   +--------------------+                   |  
      +-------------------| time slice expired |<------------------+  
      |                   +--------------------+                   |  
      |                                                            |  
      |   +------------+   +----------------+   +--------------+   |  
      +---| child      |<--| child executes |<--| fork a child |<--|  
      |   | terminates |   +----------------+   +--------------+   |  
      |   +------------+                                           |  
      |            +------------+   +-----------------------+      |  
      +------------| INT occurs |<--| wait for an interrupt |<-----+  
                   +------------+   +-----------------------+      
```

### Scheduler
- Short-term scheduler (CPU scheduler): selects which process should be executed next and allocates CPU (Ready state -> Running State)
- Short-term scheduler: which job in memory should be loaded to CPU
- Long-term scheduler (job scheduler): selects which processes should be brought into the ready queue (New state -> Ready state)
- Long-term scheduler: which job in disk should be loaded to memory
- Medium-term scheduler: selects which processes should be swapped in/out memory (Ready state -> Wait state)

### Long-term Scheduler
- Controls the degree of multiprogramming
- Executes less frequently, only when a process leaves the system or once several minutes
- Select a good mix of CPU-bound and I/O-bound processes to increase system overall performance
- UNIX/NT: no long-term scheduler, all processes are in memory

### Short-term Scheduler
- Execute quite frequently, once per 100 ms
- Must be efficient, if 10 ms for picking a job, 100 ms for such a pick => overhead = 10/110 = 9%

### Midium-term Scheduler
- swap out: removing processes from memory to reduce
the degree of multiprogramming
- swap in: reintroducing swap-out processes into memory
- Purpose: improve process mix, free up memory
- Most modern OS do not have medium-term scheduler but having sufficient physical memory or using virtual memory

## Operations on Processes

### Tree of Processes
Each process is identified by a unique process ID (PID). Processes is a tree structure.

### Process Creation
- Resource sharing
  - Parent and child share all resources
  - Child process shares subset of parent's resources
  - Parent and child share no resources
- Two possibilities of execution
  - Parent and children execute concurrently
  - Parent waits until children terminate
- Two possibilities of address space
  - Child duplicate of parent, communication viaw sharing variables
  - Child has a program loaded into it, communication via message passing

### UNIX/Linux Process Creation
- fork system call
  - Create a new (child) process
  - The new process duplicates the address space of its parent
  - Chlid & Parent execute concurrently after fork
  - Child: return value of fork is 0
  - Parent: return value of fork is child's PID
- execlp system call
  - Load a new binary file into memory, destroying the old code
- wait system call
  - Parent waits for child to complete

Memory space of fork
- Old implementation: child is an exact copy of parent
- Current implementation: use copy-on-write technique to store differences in A's child adress space. When child modifies the data, a copy of the data is made and the child process modifies its own copy. The parent process is not affected.

```c
#include <stdio.h>
void main( )
{
    int A;
    /* fork another process */
    A = fork( );
    if (A == 0) { /* child process */
        printf(“this is from child process\n”);
        execlp(“/bin/ls”, “ls”, NULL);
    } else { /* parent process */
        printf(“this is from parent process\n”);
        int pid = wait(&status);
        printf(“Child %d completes”, pid);
    }
    printf(“process ends %d\n”, A); /* only parent will run this line */
} 
```

Process Termination
- Terminate when the last statement is executed or `exit()` is called
- Parent may terminate execution of children processes by specifying its PID (abort)
  - Child has exceeded allocated resources
  - Task assigned to child is no longer required
- Cascading termination: killing (exiting) parent => killing (exiting) all its children

## Interprocess Communication
- IPC: a set of methods for the exchange of data among multiple threads in one or more processes
- Independent processes: cannot affect or be affected by other processes
- Cooperating process: otherwise
- Purposes
  - information sharing
  - computation speedup (not always true...)
  - convenience (performs several tasks at one time)
  - modularity

Communication Methods
- Shared memory
  - Require more careful user synchronization
  - Implemented by memory access: faster speed
  - Use memory address to access data
- Message passing
  - No conflict: more efficient for small data
  - Use send/recv message
  - Implemented by system call: slower speed
- Sockets
  - A network connection identified by IP & port
  - Exchange unstructured stream of bytes
- Remote Procedure Calls
  - Cause a procedure to execute in another address space
  - Parameters and return values are passed by message

### Shared Memory  
Processes are responsible for
- Establish a region of shared memory (shared memory segment)
- Determining the form of the data and the location
- Ensuring data are not written simultaneously by processes (synchronization)

Consumer & Producer Problem
- Producer process produces information that is consumed by a Consumer process
- Buffer as a circular array with size B
- The solution allows at most (B-1) item in the buffer, otherwise cannot tell whether the buffer is full or empty

### Message Passing System
Mechanism for processes to communicate and synchronize their actions  
message-passing facility provides at least two operations：
- send(message)
- receive(message)

If process P and Q want to communicate, they need:
- establish a communication link
- exchange messages via send/receive

Implementation of communication link
- physical (e.g. shared memory, HW bus, or network)
- logical (e.g. logical properties)
  - Direct or indirect communication
  - Blocking or non-blocking

#### Direct communication
each process that wants to communicate must explicitly name the recipient or sender of the communication
- send(P, message): send a message to process P
- receive(Q, message): receive a message from process Q

Properties of communnication link
- link is established automatically
- One-to-One relationship between links and processes
- link is usually bidirectional (two-way communication)

#### Indirect communication
Messages are directed and received from mailboxes (also referred to as ports)
- send(A, message): send a message to mailbox A
- receive(A, message): receive a message from mailbox A

Properties of communnication link
- Link established only if processes share a common mailbox
- Many-to-Many relationship between links and processes
- Link may be unidirectional or bi-directional
- Mailbox can be owned either by OS or processes

Problems with mailbox: Multiple processes may try to send or receive messages at the same time  
Solutions
- Allow a link to be associated with two processes at most (but this becomes direct communication)
- Allow at most one process at a time to execute a receive() operation (locking)
- Allow the system to select arbitrarily which process will receive the message

#### Synchronization
Message passing may be either blocking (synchronous) or non-blocking (asynchronous)
- Blocking send: sender is blocked until the message is received by receiver or by the mailbox
- Nonblocking send: sender sends the message and resumes operation
- Blocking receive: receiver is blocked until the message is available
- Nonblocking receive: receiver receives a valid message or a null

Buffer implementation
- Zero capacity: blocking send/receive
- Bounded capacity: if full, sender will be blocked
- Unbounded capacity: sender never blocks

### Sockets
- A socket is identified by a concatenation of IP address and port number  
- Communication consists between a pair of sockets  
- Use 127.0.0.1 to refer itself
- Considered as a low-level form of communication unstructured stream of bytes to be exchanged
- Data parsing responsibility falls upon the server and
the client applications

### Remote Procedure Calls (RPC)
- RPC abstracts procedure calls between processes on networked systems
- Stubs: client-side proxy for the actual procedure on the server
- Client Stub
  - Packs parameters into a message (i.e. parameter marshaling)
  - Calls OS to send directly to the server
  - Waits for result-return from the server
- Server stub
  - Receives a call from a client
  - Unpacks the parameters
  - Calls the corresponding procedure
  - Returns results to the caller