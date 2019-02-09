# Comments for Pipes / Shared Memory

# 4265676f6e6554686f7421

## Total: 110
## Pipes. 50

Good, except you didn't follow this code comment:

	/* 
	 *
 	 * NOTE: The solution must be recursive and it must fork
 	 * a new child for each call. Each process should call
	 * doFib() exactly once.
 	 */
In your code each process calls doFib twice

## Shared memory 60

Great!

# AAABattery
## TOTAL 120
both submissions are great.

# Bartholemew

## TOTAL 60

## Pipes 60
great

## Shared Memory
no submission

# Beep Boop

## TOTAL. 85

##  Pipes 60
excellent

## Shared Memory 25
Incorrect output but good attempt.

# BlueDroplet

## TOTAL 60

## pipes
no submission
## shared memory 60
great

# bread_santa

## TOTAL  95

## Pipes 50
This is the most amazingly wonky solution I have seen. With your solution you really don't need pipes.

## Shared Memory 45
Ditto the above, but in this one the shared memory doesn't do anything.

# CoolJoker

## TOTAL 75

## Pipes 60
good

## Shared Memory.  15

	vagrant@vagrant:/vagrant/fibonacci/tmp$ ./v 5
	ERROR SHMAT: Permission denied

you had

	SHM_R || SHM_W
	
it should have been

	SHM_R | SHM_W

# dubZ
## TOTAL 110

## Pipes 55
Good. Although since you use tail recursion pipes are not really necessary

##Shared Memory 55
same comment as above

# Galeglory

## TOTAL 60


## Pipes 
no submission
## Shared Memory  60
good


# Godyr

## TOTAL 40

neither pipes or shared memory work but did attempt.

# Icarus

## TOTAL 120
good!

# JoshG 

## TOTAL 105

## Pipes 55
You used tail recursion so you you really didn't need pipes. 

## Shared Memory  50
Same comment


# Lex
## TOTAL 40
What you have started looks good. for the pipe one after

     //parent
     waitpid(pid1,&status1,1);
     waitpid(pid2,&status2,1);

you would do the 2 pipe reads

at the end of `doFib` you would have the write.

# Maske

## TOTAL 95

## Pipes 50
You used tail recursion so you you really didn't need pipes. The task was to use pipes for communication. You keep passing info via the fib array as this shows

	vagrant@vagrant:/vagrant/fibonacci/tmp$ ./v 5
	28105 FIB 0 1 1
	I am 28105 and I called 28106
	28106 FIB 1 1 1
	I am 28106 and I called 28107
	28107 FIB 1 2 2
	I am 28107 and I called 28108
	28108 FIB 2 3 3
	Fibonacci number:5 
	
## Shared Memory 45
Same comment with this solution.

# Maxwell
## TOTAL 105

## Pipes 53
Good, except your code didn't follow this code comment:

	/* 
	 *
 	 * NOTE: The solution must be recursive and it must fork
 	 * a new child for each call. Each process should call
	 * doFib() exactly once.
 	 */
## Shared Memory 52
Same comment as above plus your code doesn't really need shared memory

# Ninja

## TOTAL 80 

## Pipes 20
Incorrect output

# Shared Memory 60
Good

# OG Zombie

## TOTAL 50

## Pipes 50
Good, except your code didn't follow this code comment:

	/* 
	 *
 	 * NOTE: The solution must be recursive and it must fork
 	 * a new child for each call. Each process should call
	 * doFib() exactly once.
 	 */
 	 
## Shared Memory
no submission

# oof
## TOTAL 75

## Pipes 15
Works perfectly but task was to use pipes

## Shared 60
ok solution

# PitchesLoveVibrato
## TOTAL 120 
both are good


# TheCatism

## TOTAL 30

## Pipes 25
incorrect answers. 
For fib(13) I get

	vagrant@vagrant:/vagrant/fibonacci/tmp$ ./v 13
	Fork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork 
	FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork 
     FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork 
     FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork
     FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork 
     FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork 
     FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork FailedFork F

## Shared Memory 5
Correct answers but does not use shared memory


# Wiggles

## Total. 75

## Pipes 55
Good, except your code didn't follow this code comment:

	/* 
	 *
 	 * NOTE: The solution must be recursive and it must fork
 	 * a new child for each call. Each process should call
	 * doFib() exactly once.
 	 */
 	 
## Shared Memory  20
Doesn't produce correct output


# Vafer

## TOTAL:  110

Pipes. 50

Good, except your code didn't follow this code comment:

	/* 
	 *
 	 * NOTE: The solution must be recursive and it must fork
 	 * a new child for each call. Each process should call
	 * doFib() exactly once.
 	 */

Here is a trace of your processes calling doFib:

	vagrant@vagrant:/vagrant/fibonacci/tmp$ ./aaa 5
	4426 calling doFib
	4427 calling doFib
	4428 calling doFib
	4429 calling doFib
	4428 calling doFib
	4427 calling doFib
	4426 calling doFib
	4430 calling doFib
	4426 calling doFib
	4425 calling doFib
	4431 calling doFib
	4432 calling doFib
	4431 calling doFib
	4425 calling doFib
	5	
	
## Shared Memory. 60

good

# zombiesnow

## TOTAL: 80

## Pipes 20
broken

## Shared Memory 60
Good