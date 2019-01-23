# 2, Operating Systems -  Processes

# review of types of OSs

OS is just a program. What happens when you turn power on?


# SUMMARY


* CPU loads boot program from ROM
* Boot program (BIOS) runs
* Bootloader runs
* OS initialization
* start initial OS  process 
* After OS initialized & running

1) CPU loads boot program from ROM (e.g. BIOS in PCs)
	    WHAT IS A BIOS??????




Need to arrange so that first instruction the CPU executes is
BIOS entry point in ROM 

(Alternatively, on boot, state machine copies ROM to pre- specified location in 	RAM...exercise for the reader...)

*  Entry point is just another hard-wired (literally) constant 
*   Initialization circuit to set PC...

![](http://zacharski.org/files/courses/cs405/images/w02d1-bios.png)

What happens after this is covered in the bootloader lab.

## Linking and loading

Let's turn to loading programs. 

Program source has symbols like

	main() A(). X. int i
	
Hardware knows addresses (absolute or relative) 

What address should program store for main, A, or X? 

Depends on where program eventually loaded

Point is – compiler, linker need to translate symbols (procedure names, constant names, loop boundaries) to addresses


See call to memset() in lab1/kernel/init.c:i386_init() v. lab1/obj/kernel/kernel.asm;
f010015d:	e8 8c 13 00 00	call	f01014ee <memset>
try objdump lab1/obj/kernel/kernel | grep memset


→ Code all depends on certain stuff landing at certain addresses 

Linker encodes these dependencies in the code 

Loader is supposed to honor these dependencies by loading at the specified address




## Linking and loading

* Link address – address where program expects to be loaded (compile- time) 
* Load address – address where actually is loaded (run-time)

Simple case: compile one source file 
linker can assign base address for “start” (e.g., 0x01000) 
all symbols assigned an address (e.g., lw A; A stored at start+0x100  → lw 0x01100) 

Put link address in header as expected load address

Problem: Each program requires to be loaded in specific address. What happens when 2 programs have the same address space?

### Variation: Position-independent code

* More general case: Link multiple .o files 
* Create each .o file by compiling one source file 
Instead of hard-coding link/load address at compile time, symbol table (in header or pointed to in header) has list of offsets of symbols (e.g., “A” = start+178), list where symbols referenced (e.g., “A used at start+47”) 
Linker assembles .o files into one executable starting at some link address; update each file accordingly 
## Put link address in header as expected load address 
[More general case – instead of specifying link address in header, specify symbol table in header; loader also updates addresses in binary]

[Another key variation: Dynamic linking – link shared library into running binary.]


![](http://zacharski.org/files/courses/cs405/images/w02d1-va.png)

We will look more at this topic when we cover memory in a few weeks.
But this gives you a rough idea of what is going on.

# 2. Process Abstraction


Main Point: What are processes? How are process, programs, threads, and address spaces related?

Up until now, we’ve drawn lots of pictures with “user level process” as a black box. 

Let’s talk about what is inside. 

2 key ideas – state and concurrency – will be major focus of next 2 main blocks of this class


## 2.2 Processes
Process: Operating system abstraction to represent what is needed to run a single program (This is the traditional UNIX definition)

more formally: 

![](http://zacharski.org/files/courses/cs405/images/w02d1-process1.png)


**Process: a sequential stream of execution in its own address space**

Alternative: Process == a program in execution. Process execution progresses in sequential fashion.



### 2.2.1 Two parts to a process

1. sequential execution: no concurrency inside a process – everything happens sequentially [we will generalize this later]
2. address space: all process state; everything that interacts with a process


QUESTION: I’m running emacs on my linux box. What state (stuff in the computer) is part of that process? ANSWER: registers, main memory, open files (in Unix), …

Point is – no other state on machine can affect that process; if that process wants to access some other state, it needs to get help from the OS


As a process executes, it changes state

* **new**:  The process is being created
* **running**:  Instructions are being executed
* **waiting**:  The process is waiting for some event to occur
* **ready**:  The process is waiting to be assigned to a processor
* **terminated**:  The process has finished execution

![](http://zacharski.org/files/courses/cs405/images/w02d1-process2.png)
![](http://zacharski.org/files/courses/cs405/images/w02d1-ProcessState.png)




**Questions**:

* For each state how many processes can be in that state?
* Only 6 transitions in the diagram
	* how many potential transitions are there?
	* Do any missing ones make sense?

## Demo

	ps   -o pid,state,command
	
what does the x and x+ mean? how might we find out (answer `man`)

## Question: process states windows 10 mac osx

Remember  

#### Program != Process

* can have multiple instances of a program running. 
* a program can invoke more than one process

## Question: what do we need to save when we want to momentarily stop a process?

![](http://zacharski.org/files/courses/cs405/images/w02d1-process3.png)


![](http://zacharski.org/files/courses/cs405/images/w02d1-process4.png)

When P1’s registers are loaded on CPU, registers contain P1’s data, pointers, stack pointer, PC, etc. → P1 is running


## Storing PCBs

Track which processes are in which states
	collection of PCBs is called a process table

One question we could ask → how to store the table?

one option

![](http://zacharski.org/files/courses/cs405/images/w02d1-process7.png)

Store these PCB and we have some that are in the ready state, some in the wait state, one running and so on. what data structure would we use to store this collection of PCBs?

simple but slow to find process.

What is a better method?   #### team question: Is there a better way?

![](http://zacharski.org/files/courses/cs405/images/w02d1-process5.png)

![](http://zacharski.org/files/courses/cs405/images/w02d1-process6.png)

Process scheduling queues

* Job queue → set of all the processes in the system
* ready queue → set of all processes residing in main memory, ready and waiting to execute.
* Device queues → set of processes waiting for an I/O device.
* Processes migrate among the various queues. 


Modern OS → which process on the ready queue should I run next? (allocate CPU to process)

## Schedulers

future talk-> more info on schedulers.


scheduler runs very frequently – milliseconds → needs to be very fast.

## Processes 

	either I/O bound → spend more time in I/O than doing computation (short CPU burst)
	CPU bound → spends more time on computation than I/O very long CPU bursts

**draw**


# WHICH APPLICATIONS/PROCESSES ARE I/O AND CPU bound?

socrative


Context Switch
When OS switches a CPU to another process it is called a context switch.

The system must save the state of the old process and load the saved state for the new process.

Context switch time is overhead →  the  system does no useful work while switching.

We are going to use the Process Control Block to save state? What should we save?