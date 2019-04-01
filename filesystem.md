# file systems

we all need to store stuff

* photos
* music files
* videos

student
* source code
* design documents
* class notes

programs
* games
*  editors etc.

For all these we have various demands for our OS.

* **Reliability** if there is a crash, a malfunction, or power off. we want the data safely stored.
* **Large capacity and low cost** we store large amounts of data
	* mp3 -- 4-5 mb
	* video files?? 
	* source code -- nothing. 1 TB of source code if printed out would be a stack 20 miles high
	* Dropbox -- 10GB store everything from my professional life.
* **high performance** access the data without delay. more critical on server. Like NETFLIX.
* **Named data** because of the large amount of data. name the data. share it across applications. write a document and send it via email.
* **controlled sharing** able to store shared data. 
### how do these influence file system design?

* **performance** some operations are expensive (moving arm of disk drive) or erasing block of SSD. we need to optimize these so we can work with sequential ranges of data.
* **naming** group things together in a directory structure. 
* **controlled sharing** file system includes meta-data about who owns which file and who can access them
* **reliability** - store checksums to detect bad blocks, introduce transactions which make some operations atomic.

### a file system is an abstraction that provides persistent named data

define persisten and named

### a file is a named collection of data in a file system

`/home/raz/Code/cs405/log/loglikelihood.c` as an example

it is a high-level abstraction that refers to an arbitrarily sized amount of data that may not be contiguous. 

#### file info has two parts

* **file metadata** file size, modification time, owner, security info, etc.
* * **file data** the data put into the file  -- just an array of untyped bytes that stores whatever info we want. a text file gets interpreted as letters, doc files get interpreted some ways, executables -- ELF format.
* **directory** a list of human readable names mapped to underlying files.
* **path** string that identifies a file or directory is called a path. separated by slashes
* **root directory** root of the tree.

more terminology  - You define

* **absolute path**
* **relative path**
* **home directory**
* **hard link** mapping between name and file -- directed acyclic graph (DAG). avoid cycles. by not linking directories oddly.

### Explain DAG
next


## Hard links and soft (or symbolic) links
#### hard links -- a mapping between a filename and the underlying file (can't be a directory)
#### soft link -- a mapping between a filename and another filename

examples

	ln  file hardLink
	ln check.ipynb link.txt
	then ls
	see that both are pointing
both point directory to the **inode** the underlying data structure of the file.

can see the inode number by using the `-i` option to ls

	ls -i

#### softlinks
lets create a file and link it

	touch sample1
	ln -s sample1 link1
	ls -l
	ls -i
	
#### deleting
deleting sample1 makes link1 invalid. deleting oirginal file 

	rm sample1
	ls -l

## current and parent directories . and ..

## mounting a drive -- ex. a usb drive

	/Volumes/usb1/Code/csr405/log/main.c
	/media/disk-1/Code/cs405/log/main.c

# API

We need to support

* open and close files
* creating and deleting
* linking
* file access read/write

## we implement file system abstraction through a series of layers.


	Application
	--------------            |
	Library                   |   FILE SYSTEM API
	--------------            |   AND PERFORMANCE
	File System               |
	---------------           |
	Block Cache               |
	---------------
	Block Device interface    |
	----------------          |
	Device Driver             |    DEVICE
	-----------------         |    ACCESS
	Memory-Mapped I/O         |
	DMA/ Interrupts           |
	-----------------
	Physical Device


Top level of stack deals w/ user level libraries, kernel-level file systems. General API. and works to minimize slow storage access.
### API and performance
#### system calls and libraries.  

LInux uses `open(), close(), read(), write()`  applications can use
Device access -- ways for OS to access range of I/O devices. but applications can use `fopen(), fclose(), fread(), fwrite()` which aggregates system calls to read larger blocks.

**block cache** - disks slower than memory. The OS can cache read blocks and buffer written blocks to be written to the disk at a later time. It also might **prefetch** reading multiple blocks. if you read blocks 1 and 2 might prefetch the first 10.

#### Prefetch benefits include
* reduced latency
* reduced device overhead 
* improved parallelism

#### to aggressive Prefetch problems
* cache pressure - evicting blocks that are currently being used.
* I/O contention
* wasted effort


# Device drivers - common abstractions

Device drivers translate between high level abstractions implemented by the OS and the hardware specific details of I/O devices. 

* my laptop (March 2011): keyboard (x3), mouse, disk, display, ethernet, 802.11, Bluetooth, microphone, speaker, wireless microphone, touch pad (x2), status lights, printer, scanner, MP3 player, camera
* Fortunately, a common way to deal with all of these

## Low level I: Memory mapped IO
[simplified story:]

* allocate a region of physical memory for each device
e.g., "whatever device is plugged into slot 7 of my PCI will get addresses 0x10007000 to 0x10007FFF"
*  Writes/reads to this range of addresses get sent to device -- "control registers"
	* device can treat these writes/reads as requests
e.g., write value v to address 0x10070F0 means "move disk arm
to location v"
	* e.g., read from address 0x10070F4 returns current disk arm
position
n
*  Can create arbitrary function calls by setting arguments and then "go"

2 modes

 1.  Programmed IO -- read/write individual "control registers" one word at a time
2.  DMA -- hand an address, length to IO device; IO device reads/writes an array of bytes at that address

#### Direct Memory Access
Many I/O devices including most storage devices transfer data in bulk. For ex., an OS doesn't just read a word or two from disk, they usually transfer a KB or 2 at a time. Rather than the CPU doing all this work, I/O devices can use direct memory access. With this, I/O devices copy a block of data from their own internal memory to the systems main memory.


![](http://zacharski.org/files/courses/cs405/images/iodevice.png)


![](http://zacharski.org/files/courses/cs405/images/device2.jpg)
QUESTION: Should DMA get a physical address or virtual address? 

QUESTION: If DMA gets a virtual address, what happens if OS changes mappings for that page?
typically "pin" pages shared by OS and devices

### Low level II: Interrupts
IO device can interrupt kernel when something interesting happens. I/O devices are significantly slower than the OS so it better for devices to use interrupts.

* kernel handler runs and uses device driver to access IO device
* hardware has multiple interrupt numbers so that different handlers can be called for different devices


### Medium level: Device drivers
Specific control registers and "function calls" are hardware specific. Vendor produces a device driver which exports some higher-level procedural interface to OS

*  Possibly a standard interface e.g., "Standard IDE disk", "standard ethernet"
*  Possibly a standard interface + optional extensions/enhancements
*   possibly non*-standard
*   High level: Standard abstractions
*   file system hides details of disk devices -- sockets hides details of network devices ...


### Putting it all together a simple disk request.
* process issues a system call like `read()` to read data from disk into process memory
* the OS moves the calling thread to **wait queue**
* OS uses memory mapped I/O both to tell disk to read the requested data and to set up DMA so that the disk can place the data in the kernel's memory
* disk reads the data and DMAs it into main memory.
* once that is done the devices triggers an interrupt.
* the OS interrupt handler copies data from the kernel buffer to the process's address space.
* OS moves the thread to the **ready queue**


# The device itself -- magnetic disk.

![](http://zacharski.org/files/courses/cs405/images/disk1.png)

* **Disk surface**: circular disk, coated with magnetic material
* **Tracks**: concentric rings around disk surface, bits laid out serially along each track
* Each track is split up into **sectors**: arc of track; also, minimal unit of transfer
* Disks spin continuously
Disks organized as set of platters in a stack
![](http://zacharski.org/files/courses/cs405/images/disk2.png)

* Disk is read/write via a comb – 2 read/write “heads” at end of each “arm”
* cylinder – corresponds to track on each surface
* Disk operation is in terms of radial coordinates (not x, y, z)
* move arm to correct track, wait for disk to rotate under head, select head, then transfer as it is going by

A single track hard drive
![](http://zacharski.org/files/courses/cs405/images/disk3.png)

* **track has 12 sectors**
* **each sector 512 bytes**
* right now arm over sector 6

suppose we want to read sector 3. 

#### single track latency -- rotational delay. 
If the full rotational delay is R then the rotational delay is R/.75

### Multiple tracks
![](http://zacharski.org/files/courses/cs405/images/disk4.png)

Each platter has multiple tracks, if I am currently at 30 and want to go to 9. There is the amount of time it takes to move the arm to that position. and a short wait for the arm to stop jittering. that is called seek time.

### Then there is read time. -- transfer rate.

## Cache
Another component is the cache used to hold date read from or written to the disk. For example, if there is a read for one sector, the HD might read all the sectors on the track.

![](http://zacharski.org/files/courses/cs405/images/harddrive.png)

#### team work - modern disk

#### also how long it takes (in ms ) to make one complete revolution
#### also average time for I/O

$$T_{I/O} = T_{seek} + T_{rotation}  + T_{transfer} $$



### Simple model
To read or write a disk block: seek + rotation + transfer

1. seek: position heads over cylinder (+ select head)
Time for seek depends on how fast you can move the arm. Typically ~10-20ms to move all the way across disk. **move the arm and wait for it to stop wobbling.**
Typically 0.5-1ms to move 1 track (or select head on same cylinder) “Average” typically 5-7ms to move 1/3 across disk
NOTE: potentially misleading/pessimistic – assumes no locality 
2. rotational delay wait for sector to rotate underneath head
10000 RPM – 166 revolutions per second6 ms per rotation
3. transfer bytes
0.5 KB/sector * (100-1000)sectors/revolution * 166 Rev per second = 8-80 MB/s
(e.g. 10 MB/s.5KB sector0.005ms) overall time to do disk I/O
seek + rotational delay + transfer
Question: are seek and rotational Seek and rotational delay are latency transfer rate is BW

###  Caveats/Estimating performance
Modern disk drives more complex -- track buffer, sector sparing, etc. see "Introduction to Disk Drive Modeling" http://www.hpl.hp.com/research/ssp/papers/IEEEComputer.DiskMode l.pdf
avg seek time seldom seen in practice -- assumes no locality
-- assumes no scheduling


### Moving the disk head and waiting for the platter to rotate is expensive.
want a disk scheduling algorithm.

#### FIFO - first in first out -- yields poor performance
#### SPTF/ SSTF
shortest positioning time first or shortest seek time first. a **greedy scheduler** given the current position of the head and platter service the request that can be handled in the minimum amount of time. -- can cause starvation.

## Elevator based algorithms
### SCAN  
disk arm sweeps from inner to outer tracks servicing requests. then goes back the other way.
### CSCAN - 
disk arm sweeps from inner to outer and then immediately zooms back to inner.
idea is if you just reverse you have already serviced those requests. so sparse.

### R-SCAN R-CSCAN -- allows small wrong directions if the rotation will be fast.

examples

![](http://zacharski.org/files/courses/cs405/images/scan.jpg)

> Written with [StackEdit](https://stackedit.io/).
