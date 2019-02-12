# Threads

## 1. review of processes

![](http://zacharski.org/files/courses/cs405/images/threads1.png)


## limitations

can only execute on one core of a cpu at a time.
![](http://zacharski.org/files/courses/cs405/images/threads2.png)

if we want the process to execute on multiple CPUs. -- that process needs multiple execution contexts. we call these contexts threads.

![](http://zacharski.org/files/courses/cs405/images/threads3.png)


## Preview

* What are threads?
* How threads different from processes
* What data structures are used to implement and manage threads.

## threads

* execute a unit of work of a process
* works simultaneously with other threads -- many threads executing
* requires coordination. -- sharing I/O devices, memory, CPU core

## Process vs. Thread
![](http://zacharski.org/files/courses/cs405/images/threads4.png)


## Benefits of threads

Why are threads useful?
![](http://zacharski.org/files/courses/cs405/images/threads5.png)
 
each thread can process a different part of the matrix.

all. the threads execute the same code.

**by parallelization  -- speed up**

**specialization -- threads can be specialized -- we can give some higher priority**. also hotter cache
![](http://zacharski.org/files/courses/cs405/images/threads6.png)


## Why not write separate processes?
memory requirements much greater because we need separate address spaces.

threads require less memory.

communication among processes requires more costly interprocess communication.

## are threads useful in a single CPU or when. # of threads > # of CPUs 

![](http://zacharski.org/files/courses/cs405/images/threads7.png)


 process context switch - time consuming part is the VM to physical memory mapping.
 
## threads beneficial to both applications and the OS

![](http://zacharski.org/files/courses/cs405/images/threads8.png)

## SOCRATIVE QUIZ - team

![](http://zacharski.org/files/courses/cs405/images/threads9.png)

# What do we need to support threads?
* thread data structure -- identify threads, keep track of resource usage
* mechanisms to create and manage threads
* mechanisms to safely coordinate among threads running concurrently in the same address space


## Concurrency.
When processes run concurrently they operate within their own address space. no process can access another process's address space 

Threads on the other hand, can access the same physical memory
![](http://zacharski.org/files/courses/cs405/images/threads10.png)

This can create inconsistencies.

**data race**
![](http://zacharski.org/files/courses/cs405/images/threads11.png)

# 1 Multiprocessing v. Multiprogramming
![](http://zacharski.org/files/courses/cs405/images/threads12.png)


* Dispatcher can choose to run each thread to completion or
* time-slice in big chunks or
* time-slice so that each thread executes only one instruction at a time (simulating a multiprocessor where each CPU operates in lockstep)

If the dispatcher can do any of the above – programs must work under all cases, for all interleavings
**So how can you know if your concurrent program works?** Whether all interleavings will work?


* Option 1: enumerate and test all possibilities
Impossible!
* Option 2: maintain invariants on program state; structure program carefully to maintain these invariants

# 2 Independent v Cooperating Threads

## 2.1 Definitions

**Independent threads** no shared state with other threads

* deterministic- input state determines result
* reproducible
* scheduling order doesn’t matter

**cooperating threads** – share state • 

* non-deterministic
* non-reproducible

Non-reproducibility and non-determinism means that bugs can be intermittent. This makes debugging hard.

3 problems

1. combinatorial explosion in # possible interleavings 
2.  program execution is non-deterministic
2.  compilers and architectures can reorder operations

 ## 2.2 Why allow cooperating threads?

People cooperate; computers model people’s behavior, so at some level they have to cooperate

1. Share resources/information
	1. one computer, many users
	2. one bank balance many tellers
2. Speedup
	1. overlap I/O and computation
	2. multiprocessors – chop up program into little pieces and run them in parallel 
3.  Modularity - Chop up large problem into simpler pieces
	1. modularity example – typesetting: `ref | grn | tbl | eqn | troff`
	2. This makes the system easier to extend; you can write eqn without changing troff
3. Fundamentally required – look at thread switch example above – different threads share ready queue, scheduling data structures, ...


## 2.3 Some simple concurrent programs
Most of the time, threads are working on separate data, so scheduling order doesn’t matter

	Thread A                               Thread B
	
	x = 1;                                      y = 2
	
	What are the possible values for x?
	
	x = 1                                       x = 2
	
	What are the possible values for x: initially: y = 12
	
	x = y + 1;                                 y=y*2;

	What are the possible values for x: initially x = 0
	
	x = x + 1;                                  x=x+2;

##2.4 Atomic operations
atomic operation – always runs to completion or not at all. **indivisible** can't be stopped in the middle


On most machines, memory reference and assignment (load and store) of words are atomic

Many instructions are not atomic. For example, on most 32-bit architectures, double precision floating point store is not atomic. It involves 2 separate memory operations.

For this example, assume that memory load and memory store are atomic, but incrementing and decrementing are not atomic

	Thread A                 Thread B
	i = 0                         i = 0
	while(i < 10){.           while (i > -10){
	  i = i + 1                         i = i - 1
	  }                               }
	  print "A wins"           print "B wins"
	  
QUESTIONS

1. Who wins? (could be either)
2. Is it guaranteed that someone wins? Why or why not?
3. What if both threads have their own CPU core running in parallel exactly at the same speed. Is it guaranteed that it goes on forever?
4. Could this happen on a uniprocessor? - yes, but unlikelyl. but don't depend on it not happening.  it will eventually happen, and your system will break and it will be very difficult to figure out why.


# 3. fundamental problem
Independent v. cooperating threads

1. must work with all possible interleavings 
2. not feasible to reason about all interleavings
	1. mentally compile code down to assembly 
	2. think about every possible interleaving
	3.  [intuition is a poor guide...]

Atomic operations – a start

* but 3-line program still takes 200 work-minutes to analyze
* mentally disassemble code, compute all interleavings, ... 

### fundamental problem
concurrency breaks modularity

key idea for reasoning about programs -- modularity. Structure system so that I only need to look at this code to understand what this code will do.

but with threads I need to reason about how different pieces of code interact -- I don't just get to step though (locally) this happens, then this happens...
Challenge -- need to restore modularity to our reasoning!


# 4. Too much milk

time | Ann | Bob
:--: | :--: | :--:
3:00 | Look in fridge; out of milk.
3:05 | Leave for store
3:10 | Arrive at store | Look in fridge; out of milk.
3:15 | Buy milk | Leave for store
3:20 | Arrive home; put milk away. | Arrive at store
3:25 | - | Buy milk
3:30 | - | Arrive home; put milk away.

each person is a thread and the milk is a shared variable. 

## 4.1 Too Much Milk: Solution #1

Suppose I write a program to model the too much milk problem. People act in parallel, so model each person as a thread. Model "look in fridge" and "put away milk" as reading/writing a variable in memory.

What are the correctness properties for the too much milk problem? 

* **the safety property?** the program never enters a bad state. never more than one person buys
* **the liveliness property** the program eventually enters a good state. someone buys if needed

Restriction: only use atomic load and store operations as building blocks.

### Basic idea of solution #1
try to arrange so that only one thread is deciding/buying at a time 

1. Leave a note (kind of like “lock”) {store "1" to location NOTE}
2. Remove node (kind of like “unlock”) {store "0" to location NOTE} 
3. Don’t buy if note (wait)
{load from NOTE, BEQ...}

Solution #1

        if (milk == 0){
           if(note == 0){
             note = 1; // leave note
             milk++;   // buy milk
             note = 0; // remove note;
     } }

**Is this protocol safe?**

Why doesn’t this work? Thread can get context switched after checking note but before leaving note.

Ann | Ben
:---: | :---:
`if(milk == { )` | - 
 - | `if(milk == { )`
 - | `if(note == 0) {`
 - |`note= 1`
 - | `milk++`
 - | `note = 0`
 - | `}}`
 `if(note == 0) {` | -
 `note = 1` | 
 `milk++` | 
 `note = 0` | 
 `}}` | 

Our “solution” makes problem **worse** – fails only occasionally. Makes it really hard to debug. **heisenbug** Fails when the CEO is demoing a new product at a trade show.  Remember, constraint has to be satisfied, independent of what the dispatcher does – timer can go off and context switch can happen at any time.

## Too much milk solution #2
How about labelled notes? That way we can leave the note before checking the milk or note.

![](http://zacharski.org/files/courses/cs405/images/threads13.png)

Is this protocol **safe**? Proof sketch:

Assume for **the sake of contradiction that both A and B buy**. Consider the state of “(noteB, milk)” when thread A was at Y

* case 1: noteB = 1, milk any state – Impossible -- contradiction with assumption A buys milk and reaches X
* case 2: noteB = 0, milk > 0 ”Impossible -- we know B is not executing any of the z lines. we also know that noteA = 1 or milk > 1. so B will not by milk in the future.

if B above Z
* B is not in Z1-Z5
* noteA OR milk is a stable property
* --> B will not buy
* 
**Is it live?**
Possible for neither thread to buy milk; context switch at wrong time can lead to each thinking the other is going to buy
• Illustrates **starvation**: thread waits forever

Ann | Ben
:---: | :---:
`noteA = 1` |
= | `noteB = 1`
`if noteB == 0 | if noteA == 0`
A doesn't buy | B doesn't buy

### Solution 3

![](http://zacharski.org/files/courses/cs405/images/threads14.png)

This is both safe and live

[[ADD MORE]]



# Semaphores

## Definition
A semaphore is like an integer with three differences

1. When you create the semaphore, you can initialize its value to any integer, but after that the only operations you are allowed to perform are increment (increase by one) and decrement (decrease by one). You cannot read the current value of the semaphore.
2. When a thread decrements **wait** the semaphore, if the result is negative, the thread blocks itself and cannot continue until another thread increments the semaphore.
3. When a thread increments (**signal**) the semaphore, if there are other threads wait- ing, one of the waiting threads gets unblocked.

## syntax

	ann = Semaphores
	fred.signal()
	fred.wait()
	
## Examples

Thread A | Thread B
:--- | :---
sem = Semaphore(0) | ...
statement a1 | sem.wait()
sem.signal() | statement b1


### rendezvous
Generalize it so it works both ways

Thread A | Thread B
:--- | :---
sem = Semaphore(0) | ...
statement a1 | statement b1
statement a2 | statement b2

refer to Downey book p 15

### example mutex.

### example multiplex.

