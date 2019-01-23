# W01L1 Introduction to Operating Systems

## welcome
self-driving car

## teams

* section 1  25 - 4 women - 5 teams
* section 2 17  - 3 teams

Questions

* C experience beyond 305
* pretty comfortable w/ thread programming
* expert at using Unix command line 
* other

team deliverable

avatar 

## CS305 team questions
* Why study computer architecture?
* How does it help you as a software developer (if at all)?

team report and discuss

OS might be the same

* Know how stuff works (takes the mystery out of things)
* some stuff that is conceptually interesting and beautiful
* counterpoint story


## C programs

## cpu.c: 
just something you can run one instance of first, and
       then a bunch of at once, in order to show that the CPU
       can be VIRTUALIZED. Amazingly, the OS can make it seem
       like you have as many CPUs as you need!
       e.g., prompt> cpu A

## threads.v0.c:
shows that a concurrent program, when run without locks,
       does weird stuff. explain how the program should work,
       and then ask the students what should happen.

## mem.c: same thing 

## io.c

	strace -c umem (interruptions)
	w/o -c
	show mac trace
	

       
##  trying writing on paper



## Preview

* what is an operating system
* what are the key components of an operating system?
* Design and implementation considerations of OS.
* Architecture
* Course Logistics


# What is an operating system?

An operating system implements a virtual machine that is (hopefully) easier to program than the raw hardware.

## Simple OS Definition

A special piece of software that...

* abstracts and 
* arbitrates

the underlying operating system.

**abstract** means to simplify what the hardware looks like

**arbitrate** means to manage

![](http://zacharski.org/files/courses/cs405/images/w01d1-fig2.png)


In some sense: OS is just a software engineering problem: how do you convert what the hardware gives you into something application programmers want?


For any OS area (file systems, virtual memory, CPU scheduling) begin by asking two questions:

What’s the hardware interface? (The physical reality) 

What’s the application interface? (The nicer abstraction)


Of course, should also ask why the interfaces look the way they do, and whether it might be better to push more responsibilities into applications, the OS, or hardware.


# 2.  3 functions of an OS: 
1. **referee**: manage resources among applications - is this the abstract or arbitrate role?
2. **illusionist** - abstraction of hardware (don’t need details of graphics card to write hello world, is the arbitrate or abstract role 
3. **glue** - share among applications -> file system. cut and paste

# MORE DETAIL 
## referee

### Phone

* map
* music
* phone call

in relation to speaker

### laptop in relation to display

* browser
* browser playing Netflix
* terminal window
* editor
* 

running on my laptop - browser, terminal window. etc. all those are running - and keep all of them separate. efficiently . etc.  **this referee role has some sub components.**

### resource allocation. 
allocate memory, network, processors, to the running applications. we don’t want compiling a long program to interfere with us watching a video on VLC. or streaming netflix. 

without an OS it would be difficult to have multiple programs running at the same time.

### one example, we don't want one program to have access to another's memory

### isolation
events in one program should not interfere with another.
part of this is called **fault isolation** -> an error in one program should not interfere with another or even the OS.   true with your Android phone. part of this is limiting what programs can do. memory access, restrict access to certain CPU instructions, etc. 

### communication
if everything were completely separate. life would be easy.
sometimes we want to share. if we have a large task we might want to distribute the work across programs. share info.  how might an OS allow sharing of info?

*This was all under the heading of OS as referee *

## OS as illusionist
what do you think that means?

**one method** -> virtualization.
meaning OS gives the illusion that an application has a complete dedicated machine.

We saw this in the programs we ran at the beginning of class (dedicated machine). Those also illustrated the referee role 
ATOMIC OPERATIONS -> Multiple cores - changing same memory location

**push this idea further**-> virtualize the entire computer. so we can run another operating system as an application on top of the existing OS.

![](http://zacharski.org/files/courses/cs405/images/w01d1-fig3.png)

why might we want to do this?

distribute identical OS across computers.  

* UTexas lab space
* Docker images to increase throughput
* security. some security experts -> use one browser for banking and nothing else.


other illusion roles -> atomic memory -> disk writes

**here’s a question -> let’s say we have machines with 16gb of memory. how might we provide the idea of infinite memory to an application?**

Q2: let’s say we want to update a complex data structure on disk in consistent fashion despite disk crashes.


## Finally had role of OS as glue.

glue in a number of ways. switch from spinning disks to SSD and application doesn’t need to change.

**Standard Services**: provide standard facilities that everyone needs (e.g. standard libraries, window system, file cache, ...)
“standard library” or “standard utilities” don’t run in kernel, but can be regarded as part of OS

What if you didn’t have an OS? Source code → compiler → object code → hardware


How do you get object code onto the hardware? 
How do you print an answer? 
Before OS’s: toggle in program in binary; read out answers from LED’s! 

**Simple OS**: What if only one application at a time?
Examples: very early computers, early PC’s, embedded controllers (elevators, cars, Nintendos, …) iPhone


**PICTURE OF MSDOS**

Worry less about coordination, more about standard services

then OS is just a library of standard services. Examples: device drivers, interrupt handlers, math libraries, etc

History of OS – for each new platform, OS starts as standard services (b/c only run one job at a time), evolves into “real” os that does coordination too Mainframe, PC, palmtop?, cell phone?, …


##  More complex OS: what if we share machine among multiple applications?

Then OS must manage interactions between different applications and different users for all hardware resources: CPU, physical memory, I/O devices (disks, printers, screen, keyboard), interrupts, etc

Of course, OS can still provide library of standard services 
Discussion: What are key OS services? Do they do (1) coordination (resource mgmt, security, communication) or (2) standard services

•	Example: file system – what aspects of file system are resource management? Security? Communication? Standard services?

communication among processes. 

graphical user interface library 

# There are many applications that are like operating systems.
browsers: 

do browsers have a referee role?
illusionist role?
glue role? -> javascript interpreter

database systems:
referee
illusion
glue -> common services/ syntax.

these general ideas we learn in OS we can apply in other areas.

## How big is a modern operating system

* Windows 10: 60 million sloc
* Linux 17 million
* OSX  86 million

IBM os/2 warp os team: 2000 developers

**kernel vs entire os**

## Hardware components

![](http://zacharski.org/files/courses/cs405/images/w01d1-fig4.png)

All these components will be used by multiple applications.

For example, 

* in laptops -> browser playing music, a text editor, email app, 
* On phone -> map, music, phone software
* data centers -> web server, database server, other computational devices
* self driving car

so the OS is the layer between the hardware and these programs 

for ex., ssd's and spinning disks in a variety of formats. fat32, exfat, ntfs, zfs, you code doesn't need to be aware of all this. you can just read/write -- hide complexity. provides a virtual machine.

# More formal OS definition

An OS is a layer of systems software that:

* directly has **privileged access** to the underlying hardware
* hides hardware complexity
* manages hardware on behalf of one or more applications according to predefined policies
* it ensures that applications are isolated and protected from one another

## components of an OS


#QUIZ  - socrative.com ZACJAR


1. Which of the following are likely components of an operating system? Check all that apply. DISCUSS
2. abstraction or arbitration - 3 questions.


are they core (kernel) parts
peripheral


# Operating system examples. 
depends on environment

Draw picture.  - desktop,   embedded,   phone, mainframes, 

## desktop/laptop

OS | general | Stack Overflow | Steam |
:---: | :---: | :---: | :---: | 
MS Windows | 76% | 50% | 95%
Unix | | | 
Mac OSX (BSD) | 13% | 27% | 3%
LInux | 1.5 | 23% | 1%
ChromeOS | 1.5 | - | -
 
* Microsoft Windows (Microsoft founded in New Mexico)
* Unix based
	*  	Mac OSX  (BSD)
	*  Linux
## Internet Servers
* Windows 32%
* Unix 68%
*

## Super computers 100% Linux (2017)
## Smart phones
* Androids (Linux) 83% of market
*  iOS. 12%
*  Microsoft 3%
*  RIM < 1%

# Embedded 
* symbian
* FitbitOS
* Routers (WRT)
* VxWorks

This class -> focus on Linux

** How do you think these different uses change the requirements of an os?**


# OS Elements
talk first about abstractions and mechanisms

## abstractions

* processes, threads -> correspond to applications
* file, socket memory

## mechanisms
* create, schedule
* open, write, allocate

then add policies

## policies

* max number of sockets a process can use
* least recently used
* first in first out
* shortest job first

## example
![](http://zacharski.org/files/courses/cs405/images/w01d1-fig5.png)

allocate page to process

 # my example of memory leak
 
![](http://zacharski.org/files/courses/cs405/images/memoryLeak.png)


# Design Principles

## Separation of mechanism and policy
* implement flexible mechanisms to support many policies
* e.g. LRU, random

## optimize for common case
* where will the OS be used
* what will the user want to execute on that machine
* what are the workload requirements


# User/kernel protection boundary

The OS needs special privileges compared w/ user programs. So we distinguish between

* user level
* kernel level

 ![](http://zacharski.org/files/courses/cs405/images/w01d1-fig6.png)
 

### crossing from user level to kernel level ...

![](http://zacharski.org/files/courses/cs405/images/w01d1-fig7.png)
 
traps = exception or faults

also support signals


## System call flowchart
![](http://zacharski.org/files/courses/cs405/images/w01d1-fig8.png)

arguments can be passed either directly to kernel or indirectly via a register

## synchronous mode: wait until syscall completes
## asynchronous ...

# Crossing the user/kernel protection boundary

## User / kernel transitions
 only the os kernel can do some operations
 
* hardware supported
	* e.g. traps on illegal instructions or memory accesses requiring privilege (control transfers from user application to the kernel)
* involves a number of instructions.
	* takes 50-100ns to make the transition on Linux
	*  switches locatility (affects hw cache)


![](http://zacharski.org/files/courses/cs405/images/w01d1-fig9.png)
 
# Basic OS services
![](http://zacharski.org/files/courses/cs405/images/w01d1-fig10.png)

## OSs make these services available via system calls.

 system calls | Windows | Unix
 :---: | :---: | :---:
 Process Control | CreateProcess() | fork()
  | ExitProcess() | exit()
   | WaitForSingleObject() | wait()
File Manipulation | CreateFile() | open()
 | ReadFile() | read()
  | WriteFile() | write()
  | CloseHandle() | close()
  Device Management | SetConsoleMode() | ioctl()
   | ReadConsole() | read()
    | WriteConsole() | write()
    Communication | CreatePipe() | pipe()
     | CreateFileMapping() | shmget()
     | MapViewOfFile() | mmap()
     
and so on. Two different OSs but system calls are similar.

# System Calls Quiz - team - feel free to use the internet
On a 64 bit Linux Based OS, which system call is used to...

We will be using many of these when we work on our C labs.

 # OS Architecture
 Now that we know a bit about what an OS consists of let's look at the major types of OS architectures.
 
## monolithic (draw)
every service is part of the OS. 
benefit - everything included. things can be optimized. -- but large memory requirements

![](http://zacharski.org/files/courses/cs405/images/w01d1-fig12.png)



## modular (draw)
most common.  - everything added as module. so disk manager, scheduler, display. -- dynamically install new modules. -- more maintainable, smaller footprint.  -- modularity may negatively impact performance. 
![](http://zacharski.org/files/courses/cs405/images/w01d1-fig13.png)
## Microkernel (early Mac OS)
kernel supports just basic services.(everything else runs in user space - file system, disk drivers, etc)
This requires lots of interactions. switching between kernel and user.


![](http://zacharski.org/files/courses/cs405/images/w01d1-fig11.png)
benefits
* very small size
* verifiability
* good for embedded devices

minuses
* portability
* complexity of software development
* cost of user/kernel crossing. 



# Linux and Mac Architectures

![](http://zacharski.org/files/courses/cs405/images/w01d1-fig14.png)

## Linux Kernel
![](http://zacharski.org/files/courses/cs405/images/w01d1-fig15.png)


# Mac OSx

![](http://zacharski.org/files/courses/cs405/images/w01d1-fig16.png)


see wikipedia article


the Mach MicroKernel


## XNU X is not unix - 
* originally for NeXT
* hybrid kernel  both monolithic and microkernel

# QUIZ. - Windows 10 kernel


# Course logistics

