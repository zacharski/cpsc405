# Scheduling

## Basic Concepts
* **CPU-I/O burst cycle** - Process execution consists of a cycle of CPU execution and I/O wait.
* **CPU burst distribution**
What are the typical burst sizes of a process's execution?

![](http://zacharski.org/files/courses/cs405/bursttime.png)

![](http://zacharski.org/files/courses/cs405/burst2.png)

## Job Behavior

* **I/O bound jobs**: Jobs that perform lots of I/O tend to have short CPU bursts  
* **CPU bound jobs**: Jobs that perform very little I/O tend to have very long CPU bursts

 ![](http://zacharski.org/files/courses/cs405/bursttime.png)
 
Distribution tends to be hyper-exponential: Very large number of very short CPU bursts. Small number of very long CPU bursts.

## Histogram

 ![](http://zacharski.org/files/courses/cs405/burst3.png)
 

## CPU Scheduler
Selects from among the processes in memory that are ready to execute, and allocates the CPU to one of them

### When scheduling decisions take place
When a process

1. Switches from running to waiting state
2. Switches from running to ready
3. Switches from waiting to ready
4. terminates

Scheduling under 1 and 4 is nonpreemptive

All others **preemptive**

#### NonPreemptive
once the cpu has been allocated to a process, 
the process keeps the cpu until it terminates 
or switches to a waiting state.

Aka cooperative

assume a cooperative process.
let that process decide and let's not interrupt it.

# What do you think? Modern OSs preemptive or non Preemptive?

## Cooperative

* Microsoft Windows 3.x (pre 1995)
* Mac Pre OS X (1984-2000)
* Commodore 64

Preemptive

* Windows 95 on
* Mac OS X
* Linux, Symbian, Android iOS

## Dispatcher
Dispatcher module gives control of the CPU to the process selected by the short-term scheduler; this involves: 
* switching context
* switching to user mode
* jumping to the proper location in the user program to restart that program

### What do you think dispatcher latency means?
Dispatch latency – time it takes for the dispatcher to stop one process and start another running

## Outline of rest of talk

* Algorithms important for knowing history
* Algorithms in current use today
	* Multi-level feedback queue
* Algorithms that give better results but take too long to run.
* Multicore challenges


# What criteria to evaluate? How to tell a good scheduler from a not so good?

## CPU utilization - keep the CPU as busy as possible (40-90%)

## QUESTION: how busy are your CPUs? 

may have been more important when CPUs were expensive

## Throughput – # of processes that complete their execution per time unit

## Latency (avg) – average time from when a task arrives until it completes

### Maximizing throughput may not minimize latency

Example:  100 jobs arrive together. One takes 100 seconds and the rest 1.

* Running the first first and then running the rest yields and avg. latency of  149.5 s (100 + 101 + 102 …) 
* Running the short jobs first yields an avg. latency of 51.5s.

Latency is 3x better but throughput is the same. 

### maybe want to minimize variance of latency
users would be more satisfied with an UI that processes each input in 100ms than one that  usually processes input in 50ms but with an occasional 5 sec. pause

## Waiting time – amount of time a process has been waiting in the ready queue

## Response time – amount of time it takes from when a request was submitted until the first response is produced, not output  (for time-sharing environment)

## Real time guarantees – guaranteeing a certain amount of resources by a deadline.

## Optimizing criteria

Maximize

* CPU utilization
* Throughput

Minimize

* Turnaround Time
* Waiting time
* response time

## All performance related

## Non performance related

* Predictability
	* Job should run in the same amount of time regardless of total system load
	* Response times should not vary
* Fairness
	* Don't starve any processes
* Enforce priorities
	* Favor high priority processes
* Balance resources
	* Keep all resources busy

## Example - grocery store

burst time = # of items in cart

Should people be processed in the order they show up or is there a more equitable solution

# First Come First Served (FCFS) Scheduling

	Process    Burst Time
	P1              24
	P2               3
	P3               3
	
  	Suppose they arrive in the order P1, P2, P3
  	
The Gantt Chart for the schedule is

![](http://zacharski.org/files/courses/cs405/burst4.png)


Suppose that the processes arrive in the order: 

	P2 , P3 , P1   
The Gantt Chart for the schedule is: 
 avg. wait time?  Better or worse than previous ordering?

**called the convoy effect - short process after long one**

# Shortest-Job-First (SJF) Scheduling

Associate with each process the length of its next CPU burst.  Use these lengths to schedule the process with the shortest time

## Two Schemes

* **nonpreemptive** – once CPU given to the process it cannot be preempted until completes its CPU burst
* **preemptive** – if a new process arrives with CPU burst length less than remaining time of current executing process, preempt.  This scheme is know as the  **Shortest-Remaining-Time-First (SRTF)**

![](http://zacharski.org/files/courses/cs405/burst5.png)

## SRTF (preemptive)

	Process   Arrival Time.  Burst Time
	P1.            0.                  6
	P2.            0                   8
	P3             1                   7
	P4             2                   3
	
# Team Work

Algorithms 

* FCFS
* Nompreemptive SJF
* SRTF

on these processes

	Process	Arrival Time	Burst Time
	P1                 0               7
	P2                 2.              4
	P3                 4               1
	P4                 5               4	
	
## Summary
Shortest Job First optimal

Associate with each process the length of its next CPU burst.  Use these lengths to schedule the process with the shortest time

### Anyone see a problem with the algorithm?

![](http://zacharski.org/files/courses/cs405/burst6.png)

Questions:

* With alpha = 1
* With alpha = 0
* Good estimate is alpha = 1/2
* each successive term has less weight than its predecessor

![](http://zacharski.org/files/courses/cs405/burst7.png)

![](http://zacharski.org/files/courses/cs405/burst8.png)

# Round Robin

* Each process gets a small unit of CPU time (time quantum), usually 10-100 milliseconds.  
* Once this time has elapsed, the process is preempted and placed at the end of the ready queue.
* If there are n processes in the ready queue and the time quantum is q, then no process waits more than __ time units.

To implement RR:  (**stack or queue**)

* we keep the ready queue as a FIFO (LIFO?) queue of processes. 
* New processes are added to the tail of the ready queue. 
* The CPU scheduler picks the first process on the ready queue, sets a timer to interrupt after 1 time quantum, and dispatches the process. 

Then one of two things will happen:
* The process will have a CPU burst of less than 1 quantum and process releases CPU voluntarily
* If the process has a CPU burst greater than 1 quantum the timer goes off causing an interrupt to the OS. Context switch occurs and process gets put on tail of queue

## Choosing the quantum

* Very large—degenerates to which scheduler?
* Very small—dispatch time dominates
* Rule of thumb—for better turnaround time, quantum should be slightly greater than time of 'typical job' CPU burst. (80% of bursts should be shorter than time quantum)


![](http://zacharski.org/files/courses/cs405/burst9.png)

## example of RR with quantum = 20

	Process. Burst Time
	P1.         53
	P2.         17
	P3          68
	P4          24
	
Gantt chart


higher average turnaround than SJF but better response time

# Priority Scheduling

* Prefer one process over another
* One common implementation
	* A priority number (integer) is associated with each process
	* OS schedules the process w/ the highest priority (smallest integer = highest priority)  - mac/linux -20 to 20
* SJF is a priority scheduling where priority is the predicted next CPU burst time.
* nice renice

### Anyone see any problems with this?

* Problem: Starvation – low priority processes may never execute * Solution: Aging—as time progresses increase the priority of a process.

# Multilevel Priority Queue
* Ready queue is divided into n queues, each w/ its own scheduling algorithm, e.g.,
	* Foreground (interactive) – RR
	* Background – FCFS
* Now need to schedule between queues

## Linux 2.4 to Linux 2.6 kernel

* Linux 2.4 all processes one ready queue
* When scheduler ran it looked for the highest priority job on the queue.
* What do people think?

## Scheduling done between queues
* Fixed priority scheduling (serve all from foreground then from background)	* Doesn't solve starvation
* Time slice – each queue gets a certain amount of CPU time which it can schedule among its processes. e.g.
	* 80% to foreground in RR
	* 20% to background in FCFS.

## Multilevel Scheduling Design

How to avoid undue increase in turnaround time for longer processes when short new jobs regularly enter the system

* **Solution 1**: vary preemption times according to queue
Processes in lower priority queues have longer time slices.
* **Solution 2**: promote a process to a higher queue
After it spends a certain amount of time waiting for service in its current queue move it up
* **Solution 3 **...allocate fixed share of CPU time to jobs
If process doesn't use its share give it to  other processes
	* for, ex. Linux Q=200ms 
Variation on this idea: lottery scheduling
Assign a process “tickets” (# of tickets is share)
Pick random number and run the process w/ the winning ticket


### Multilevel Queue Scheduler

![](http://zacharski.org/files/courses/cs405/burst10.png)


* Processes permanently 
assigned to one queue
based on some property
* each queue has own 
scheduling algorithm
* scheduling among 
queues:
	* absolute
	* timeslice


### Multilevel Feedback Queue Scheduler

* A process can move between the various queues; aging can be implemented this way
* Multilevel-feedback-queue scheduler defined by the following parameters:
	* number of queues
	* scheduling algorithms for each queue
	* method used to determine when to upgrade a process
	* method used to determine when to demote a process
	* method used to determine which queue a process will enter when that process needs service

### Example

Three queues
* q0—time quantum 8 milliseconds
* q1—time quantum 16 milliseconds
* q2—FCFS 

### Scheduling
* A new job enters queue q0, which is served RR. When it gains CPU, job receives 8ms. If it doesn't finish, it is moved to queue q1
* At q1 job is again serviced RR and receives 16 additional ms. If it does not complete it is preempted and moved to queue q2.
* At q2 the job is serviced FCFS 

![](http://zacharski.org/files/courses/cs405/burst11.png)


# Worksheet