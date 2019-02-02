# 1. Ninja

## Version 2
### xp 10

PASSED test 3
FAILED tests 1, 2, 4 5, 6, 7, 8, 9, 10
your program does not produce correct output for test1 (see version1 comments)

### Segmentation fault:

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test5.sh 
	./test5.sh: line 1:  2252 Segmentation fault      (core dumped) ./a < ../	
	test5.txt > a5

### Stack smash

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test8.sh 
	*** stack smashing detected ***: <unknown> terminated
	./test8.sh: line 1:  2259 Aborted                 (core dumped) ./a < ../	test8.txt > a8

## Version 1
### XP 10

Your program doesn't produce the correct output even on the sample given in the writeup:

### Sample Input

	C# E Db G#
	1
	D E# D A
	-1
	***

### Sample Output

	D F D A	
	C# E C# G#
	
### Your output

	D E# D A 
	Db Fb Db Ab	


The part that maybe you didn't notice was "each represented as in the list of 12 given at the start of this problem.:"

# 2. ThiccBoi

## XP: 10
program segfaults on the first test case:

./test1.sh 
./test1.sh: line 1:  1868 Segmentation fault      (core dumped) ./a < ../test1.txt > a1
0a1,4

# 3. CoolJoker

## version 2
### xp  38

PASSED: tests 1 and 8
FAILED: the rest
your program does not terminate upon three asterisks as in the spec
Your program doesn't handle Ab and others correctly 

## Version 1
## XP  10
Your program doesn't produce the correct output in any of the test cases. For example:

### Sample Input

	Ab C E F G C G#
	0
	Ab C E F G C G#
	5
	***
	C E G
	3

### Expected Output

	G# C E F G C G# 
    C# F A A# C F C#
	
		
	
### Your output

	G C E F G C G# 
	C F A A# C F C# 
	D# G A# 

# 4. Beep Boop
## Version 2
### xp 38


Your program should not print out the cpu time.

Your program hangs on one test

Otherwise good.

## Version 1
### XP: 20

Even on the first test your program produces an infinite loop:

	< Error: Not a valid note:***
	< Error: Not a valid note:***
	< Error: Not a valid note:***
	< Error: Not a valid note:***
	<

When I fix that your code your code passed 3 test cases. Then:

	./test5.sh 
	*** stack smashing detected ***: <unknown> terminated
	./test5.sh: line 1:  2124 Aborted                 (core dumped)

# 5. Kozmik
### XP 20
Your program adds blank lines between the output lines which was not in the specification.  Your program produces incorrect results on several test cases including

### Sample Input

	Ab C E F G C G#
	0
	Ab C E F G C G#
	5
	***
	C E G
	3

### Expected Output

	G# C E F G C G# 
    C# F A A# C F C#
	
		
	
### Your output

     Ab C E F G C G# 

     C# F A A# C F C# 

Most serious:

	./test5.sh 
	*** stack smashing detected ***: <unknown> terminated
	./test5.sh: line 1:  2165 Aborted                 (core dumped)

I stopped testing after that.

# 6. dosdude1
### xp 0
Will not compile:

	vagrant@vagrant:/vagrant/transpose/tmp$ gcc transpose.c -o a
	transpose.c: In function ‘main’:
	transpose.c:15:5: error: variable-sized object may not be initialized
	     const char *seq[SEQ_SIZE] = {"A", "A#", "Bb", "B", "C", "C#", "Db", "D", "D#", "Eb", "E", "E#", "F", "F#", "Gb", "G", "G#"};
	     ^~~~~
	...

	transpose.c:16:5: error: variable-sized object may not be initialized
	     const char *seq2[MAX_NOTE + 1] = {"A", "A#", "B", "C", "C#", "D", "D#", "E", "F", "F#", "G", "G#"};
	     
	  transpose.c:17:5: error: variable-sized object may not be initialized
	     int seqIndex [SEQ_SIZE] = {0, 1, 1, 2, 3, 4, 4, 5, 6, 6, 7, 8, 9, 10, 10, 11, 12};
	    

# 7. Maske

### XP: 70

ran through all test cases. faster than reference solution

# 8. Icarus

## Version 2
### XP 38

PASSES 1, 5, 8, 9

FAILS 2, 3, 4, 6, 7, 10 

Fails on 

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test2.txt 
	Ab C E F G C G#
	0
	Ab C E F G C G#
	5
	***
	C E G
	3vagrant@vagrant:/vagrant/transpose/tmp$ ./a <  ../test2.txt 
	A C E F G C G#
	D F A A# C F C#****

Fails on 

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test3.sh 
	./test3.sh: line 1:  2524 Segmentation fault      (core dumped) ./a < ../test3.txt > a3
	0a1,2
	> 
	> 


##  Version 1
### XP 10

passes test 1

test 2 fail with seg fault

	./test2.sh: line 1:  2294 Segmentation fault      (core dumped) ./a < ../test2.txt > a2
	0a1,2

The input was

	Ab C E F G C G#
	0
	Ab C E F G C G#
	5
	***
	C E G
	3

tests after that resulted in seg faults.

# 9. Beep Boop

## Version 2

###XP 5
your program segfaulted on first three tests

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test1.txt 
	C# E Db G#
	1
	D E# D A
	-1
	***vagrant@vagrant:/vagrant/transpose/tmp$ ./a <  ../test1.txt 
	Segmentation fault (core dumped)
	vagrant@vagrant:/vagrant/transpose/tmp$ ./a <  ../test0.txt 
	Segmentation fault (core dumped)
	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test0
	cat: ../test0: No such file or directory
	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test0.txt 
	C# E Db G#
	1
	D E# D A 
	-1
	***

## Version 1
### xp. 5

Compiling gives warnings

	vagrant@vagrant:/vagrant/transpose/tmp$ gcc test.c -o a
	test.c: In function ‘main’:
	test.c:66:13: warning: passing argument 1 of ‘strcat’ from incompatible pointer type [-Wincompatible	-pointer-types]
     	 strcat(printing, " ");
     	        ^~~~~~~~
          
  First test results in segmentation fault:
  
       vagrant@vagrant:/vagrant/transpose/tmp$ ./test1.sh 
		./test1.sh: line 1: 14258 Segmentation fault      (core dumped) ./a < ../test1.txt > a1
	0a1,2
	> D F D A
	> C# E C# G#

# 10. BadLuckOrphan


## Version 2

### xp 0
ALL TESTS FAIL
2 have stack smashing

test 1 fails:

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test1.txt 
	C# E Db G#
	1
	D E# D A
	-1
	***vagrant@vagrant:/vagrant/transpose/tmp$ ./a < ../test1.txt 
	D F D 
	C# E C#

## Version 1
### XP 20

test1 - ok but there are spaces before newline (not in spec)

test2 failed

###  Test 2 input

	Ab C E F G C G#
	0
	Ab C E F G C G#
	5
	***
	C E G
	3

### Expected Output

	G# C E F G C G#
	C# F A A# C F C#

### Your output

	C# F A A# C F C# D C# F A A# C F C# 

test3 failed

test4 ok except for the spaces

tests5 and 6 - stack smash

	*** stack smashing detected ***: <unknown> terminated
	./test5.sh: line 1: 14304 Aborted                 (core dumped) ./a < ../test5.txt > a5

test 7 fails

test 8 passes except for spaces

test 9 failed

# 11.Wiggles

## Version 2

### XP 5

failed on first test

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test1.txt 
	C# E Db G#
	1
	D E# D A
	-1
	***vagrant@vagrant:/vagrant/transpose/tmp$ ./a <  ../test1.txt 
	D F D 

	C# E C# 

on the next three tests your program hangs so I stopped testing
	
	
## Version 1
###XP 5
fails on first test

### Test1 input

	C# E Db G#
	1
	D E# D A
	-1

### Expected Output

	D F D A	
	C# E C# G#

### Your output (It hangs)

vagrant@vagrant:/vagrant/transpose/tmp$ ./a < ../test1.txt 
	Enter a set of notes: Enter number of half-steps to be transposed: D F D A 
	Enter a set of notes: Enter number of half-steps to be transposed: C# E C# A 
	Enter a set of notes: Enter number of half-steps to be transposed: 
	^C

As a result, I cannot run my test suite

# 12. bread_santa

### XP 18

compiler warning

	transpose.c:33:12: warning: comparison between pointer and integer
   	if(shift != NULL){ // makes sure shift has been initialized with user input, will throw a warning at compile t	ime but "it's fine" (read: hasn't broken yet)
   
   test 1: passed
  
    test 2: failed
   
### Test 2 input

	Ab C E F G C G#
	0
	Ab C E F G C G#
	5
	***
	C E G
	3

### Expected output

	G# C E F G C G#
	C# F A A# C F C#

### Your output

	C# F A A# C F C#

test 3, 4 failed

test 5 and 6: 

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test5.sh 
	*** stack smashing detected ***: <unknown> terminated
	./test5.sh: line 1: 14366 Aborted                 (core dumped) ./a < ../test5.txt > a5
	0a1

test 7 fail

test  8 - stack smashing

test 9 pass

# 12. Maxwell

###  XP 60
all tests passed

### Your time

	real	0m1.014s
	user	0m0.534s
	sys	0m0.439s

### reference time

	real	0m0.205s
	user	0m0.016s
	sys	0m0.163s

# 13. PitchesLoveVibrato

## Version 2

### xp 38
your program adds blank lines between output lines

test 1 results in an infinite loop:

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test1.txt 
	C# E Db G#
	1
	D E# D A
	-1	
	***vagrant@vagrant:/vagrant/transpose/tmp$ ./test1.sh 

test 6  core dump

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test6.sh > foo
	realloc(): invalid next size
	./test6.sh: line 1:  2116 Aborted                 (core dumped) ./a < ../test6.txt 	> a6

Version 1
### XP 25

there are spaces at the end of each of your output lines

* tests 1-4 passed other than the space problem
* test 5 fail - incorrect output
* test 6 Segmentation fault (core dumped)
* test 7 pass
* test 8 incorrect output
* test 9 pass


# 14. Vafer
## xp 40

* tests  1, 2, 3 pass
* test 4 failed

### Test 4 input

	A Bb C
	15
		
	5
	***
	C E G	
	3
	D E F
	1

### Expected Output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../out4
	C C# D#

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test4.txt 

### Your output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat a4
	C C# D#
	Fvagrant@vagrant:/vagrant/transpose/tmp$ cat ../out4

* test 5, 6, 7 passed
* test  8 failed
* test 9 passed
* test 10 failed


Your time

	real	0m0.213s
	user	0m0.004s
	sys	0m0.188s

reference time

	real	0m0.207s
	user	0m0.004s
	sys	0m0.175s

# 15. Raikit

### xp: 10

first test fails:

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test1.sh 
.	/test1.sh: line 1: 14546 Segmentation fault      (core dumped) ./a < ../test1.txt > a1
	0a1,2

# 16. LaoHu
### XP 5
fails test 1:

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test1.sh 
.	/test1.sh: line 1: 14570 Segmentation fault      (core dumped) ./a < ../test1.txt > a1


# 17. 4265676f6e6554686f7421 

## Version 2
### XP 38

PASSED: tests 3, 5, 6, 9
FAILED the rest


Fails on first test (similar problems on other tests):

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test1.txt 
	C# E Db G#
	1
	D E# D A
	-1
	***vagrant@vagrant:/vagrant/transpose/tmp$ ./a <  ../test1.txt 
	D F D A# 
	C# E C# G# 


## Version 1 
### XP 5
fails on every test (even if I disregard sharps and flats)

### test 1 input

	C# E Db G#
	1
	D E# D A
	-1

### Expected Output

	D F D A
	C# E C# G#

### Your output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat a1
	F
	CC

# 18. Hydron
### xp 5


Fails on first test.
### test 1 input

	C# E Db G#
	1
	D E# D A
	-1

### Expected Output

	D F D A
	C# E C# G#

### Your output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat a1
	D# D D# G#???
	vagrant@vagrant:/vagrant/transpose/tmp$ 

# 19 zombiesnow 
### xp 25

* tests 1, 2, 3 passed
* test 4 failed.

### Test 4 input

	A Bb C
	15
	
	5
	***
	C E G
	3
	D E F
	1

### Expected output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../out4 
	C C# D#

	vagrant@vagrant:/vagrant/transpose/tmp$ 

### Your output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat a4
	C C# D#
	D
	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test4.txt 

test 5 and 6:

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test5.sh 
	*** stack smashing detected ***: <unknown> terminated
	./test5.sh: line 1: 14632 Aborted                 (core dumped) ./a < ../test5.txt > a5

* test 7 passed
* test 8: stack smashing
* test 9 and 10 passed

# 20 Smitty Werben Man Jensen 

## Version 2.
### xp 55
* tests 1 & 2 passed
* test3 & 4 failed

	vagrant@vagrant:/vagrant/transpose$ cat test3.txt 

	0

	5
	***
	C E G
	3


* tests 5 - 10 passed

Time test


	time ./a < ../test6.txt > foofoo
	
	real	0m0.194s
	user	0m0.000s
	sys	0m0.173s

## Version 1.
### xp 5
* test 1 fails

### test 1 input

	C# E Db G#
	1
	D E# D A
	-1

### Expected Output

	D F D A
	C# E C# G#

### Your output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat a1
	C# E C# G#
	E C# G#


#  21 Bartholemew

## Version 2
### xp 5

FAIL: all except test3

PASS: test3


## Version 2
## xp 5
test 1 fails

### test 1 input

	C# E Db G#
	1
	D E# D A
	-1

### Expected Output

	D F D A
	C# E C# G#

### Your output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat a1
	D F D A 
	Db Fb Db Ab 

# 22 oof
### xp 40
program add spaces before newlines in output

other than that tests 1, 2, and 3 pass

test 4 fail

test 5 and 6 pass

test 7-10 fail



#  23 Boba2
### xp 5
test 1 fails
     
### test 1 input

	C# E Db G#
	1
	D E# D A
	-1

### Expected Output

	D F D A
	C# E C# G#

### Your output
     	vagrant@vagrant:/vagrant/transpose/tmp$ cat a1
	D F A 
	
	D# F D# A# 

	vagrant@vagrant:/vagrant/transpose/tmp$ 


# 24 dubZ

## Version 2

### xp 35

fails on 4 tests
fails on (and a similar test)

   vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test2.txt 
	Ab C E F G C G#
	0
	Ab C E F G C G#
	5
	***
	C E G
	3vagrant@vagrant:/vagrant/transpose/tmp$ cat ../out2
	G# C E F G C G#
	C# F A A# C F C#
	vagrant@vagrant:/vagrant/transpose/tmp$ ./a < ../test2.txt 
	Ab C E F G C G#
	C# F A A# C F C#

fails on (also seg faults on another test)

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test7.sh 
	./test7.sh: line 1:  2494 Segmentation fault      (core dumped) ./a < ../test7.txt > a7
	0a1,3
	> C# E C# C# G#
	> D F D A G#
	> 
	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test7.txt 
	C# E Db C# G#
	12
	D E# D A Ab
	-12 
	***

## version 1
###xp 25
* test 1 pass
* test 2 fail

### test 2 input

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test2.txt 
	Ab C E F G C G#	
	0
	Ab C E F G C G#
	5
	***
	C E G
	3

### Expected output

	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../out2 
	G# C E F G C G#
	C# F A A# C F C#
	vagrant@vagrant:/vagrant/transpose/tmp$ 

### Your Output
	vagrant@vagrant:/vagrant/transpose/tmp$ cat a2
	Ab C E F G C G#
	C# F A A# C F C#

* tests 3, 4, 5, passed
* test 6 & 7, 9 fail with seg fault

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test6.sh > foofoo
	./test6.sh: line 1: 14814 Segmentation fault      (core dumped) ./a < ../test6.txt > a6

* 8 passes
* 10 fails

# 25 FburgUMW

## Version 2

### xp 25
you have a space at the end of each output line

SEGMENTATION FAULTS: tests 3, 4, 6, 8

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test3.sh 
	./test3.sh: line 1:  2026 Segmentation fault      (core dumped) ./a < ../			test3.txt > a3
	0a1,2
	> 
	> 
	vagrant@vagrant:/vagrant/transpose/tmp$ cat ../test3.txt 
	
	0
	
		5
	***
	C E G

##version 1
### xp 5
compiler warnings


test 1 - failed - minor but you have a space at the end of each output line

test 2 through 4 failed due to seg fault

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test2.sh 
	./test2.sh: line 1: 14859 Segmentation fault      (core dumped) ./a < ../test2.txt > a2
	0a1,2

test 5,6,8 failed due to stack smashing

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test5.sh 
	*** stack smashing detected ***: <unknown> terminated
	./test5.sh: line 1: 14876 Aborted                 (core dumped) ./a < ../test5.txt > a5

test 7 failed - seg fault

test 9 passed

test 10 seg fault


# 26 BlueDroplet

### xp 5

first test failed

	vagrant@vagrant:/vagrant/transpose/tmp$ ./test1.sh 
	a: malloc.c:2868: mremap_chunk: Assertion `((size + offset) & (GLRO (dl_pagesize) - 1)) == 0' failed.
	./test1.sh: line 1:  1266 Aborted                 (core dumped) ./a < ../test1.txt > a1
	0a1,2
	> D F D A
	> C# E C# G#


# AAABattery

## XP 5
All tests failed (some would have passed if you put spaces between notes)

