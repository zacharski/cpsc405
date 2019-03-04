# Parallel Log Likelihood 

In the syllabus this was listed as a parallel zip program with a difficulty rating of 3. Today, in an effort to minimize the likelihood of finding complete solutions on the web, I started looking at other compression algorithms. My goal was to find one that was a bit more obscure, the algorithm was straight forward to understand, and there would be some obvious way to parallelize it. That proved quite difficult, so I switched to this task, which has the same objectives (plus I am hoping you will find it fun)

## Objectives

There are three specific objectives to this assignment:

* To familiarize yourself with the Linux pthreads.
* To learn how to parallelize a program.
* To learn how to program for high performance.

## Background

To understand how to make progress on this project, you should first
understand the basics of thread creation, and perhaps locking and signaling
via mutex locks and condition variables. These are described in the following
book chapters:

- [Intro to Threads](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-intro.pdf)
- [Threads API](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-api.pdf)
- [Locks](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks.pdf)
- [Using Locks](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks-usage.pdf)
- [Condition Variables](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-cv.pdf)

Read these chapters carefully in order to prepare yourself for this project.

It may also be useful to complete the [HackerRank Ransom Note Problem](https://www.hackerrank.com/challenges/ctci-ransom-note/problem). If you show me your hackerrank score for that problem I will give you that in xp.

## The task

Ted Dunning wrote an often cited and simply wonderful paper [Accurate Methods for the Statistics of Surprise and Coincidence ](http://aclweb.org/anthology/J93-1003) that argued that many of the statistics people use for text analysis are not accurate. In that paper he proposes a method called log likelihood. We can use this method to find out how 'surprising' a word is in one text compared to another.  For example, I compared speeches Trump gave when running for president to those given by Obama. Here are the  top words sorted by log likelihood:

word | log likelihood
:---: | :---:
going | 274.71
I | 249.47
very | 163.82
who | -117.30
the | -114.38
really | 83.01

We interpret this as meaning that it is somewhat surprising how many times Trump used the word *going* given how often that word occurs in Obama's speeches. It is also surprising that Trump uses *I* and *very* as often as he does. On the other hand, it is surprising that Obama uses *who* and *the* as often as he does given how often those words appear in Trump's speeches. 

### 8 years ago in PHP
8 years ago, I implemented this in PHP. (back then, it was still in the days when PHP was cool)The sites is [Online Text Comparator](http://guidetodatamining.com/ngramAnalyzer/comparator.php). The source code is in [this github repo](https://github.com/zacharski/ngramAnalyzer).  On the bottom of the webpage you can try some texts I uploaded and compare the Lotus Sutra to Walden, for example.

# Your task: a parallel implementation.
You are to implement the formula Dr. Dunning gives to compare 2 texts. For example:

	vagrant:/log-likelihood lotus.txt walden.txt > output.csv 

All output should be written to standard output.  The output should be sorted by the absolute value of log likelihood and be in the following comma-separated format. (The column names must be those specified).

	Word,lotus.txt,walden.txt,Log Likelihood
	Buddha,1047,0,1504.6021538156
	Law,584,1,831.8702926808
	will,1297,272,793.33621355379
	beings,568,4,779.70931071183
	which,88,860,687.13483780937
	Buddhas,466,0,662.71228351799
	I,755,1988,511.93738695956
	a,1431,2950,462.81063218624
	sutra,325,0,459.20756494149
	One,511,55,451.42778525577

which looks like the following in table form.

Word|lotus.txt|walden.txt|Log Likelihood
:--: | :--: | :--: | :--: |
Buddha|1047|0|1504.6021538156*
Law|584|1|831.8702926808
will|1297|272|793.33621355379
beings|568|4|779.70931071183
which|88|860|687.13483780937
Buddhas|466|0|662.71228351799*
I|755|1988|511.93738695956
a|1431|2950|462.81063218624
sutra|325|0|459.20756494149*
One|511|55|451.42778525577

The numbers in the lotus.txt and walden.txt columns are the number of times that word appeared in that text file.


Internally, the program will use POSIX threads to parallelize the process.  

## Considerations

Doing so effectively and with high performance will require you to address (at
least) the following issues:

- **How to parallelize the task.** Of course, the central challenge of
    this project is to parallelize the process. Think about what
    can be done in parallel, and what must be done serially by a single
    thread, and design your parallel program as appropriate.

    One interesting issue that the "best" implementations will handle is this:
    what happens if one thread runs more slowly than another? Does your algorithm give more work to faster threads? 

- **How to determine how many threads to create.** On Linux, this means using
    interfaces like `get_nprocs()` and `get_nprocs_conf()`; read the man pages
    for more details. Then, create threads to match the number of CPU
    resources available.

- **How to efficiently perform each piece of work.** While parallelization
    will yield speed up, each thread's efficiency in performing its work
     is also of critical importance. Thus, making the core
     loop as CPU efficient as possible is needed for high
    performance. 

- **How to access the input file efficiently.** On Linux, there are many ways
    to read from a file, including C standard library calls like `fread()` and
    raw system calls like `read()`. One particularly efficient way is to use
    memory-mapped files, available via `mmap()`. By mapping the input file
    into the address space, you can then access bytes of the input file via
    pointers and do so quite efficiently. 


## Grading

Your code should compile (and should be compiled) with the following flags:
`-Wall -Werror -pthread -O`. The last one is important: it turns on the
optimizer! In fact, for fun, try timing your code with and without `-O` and
marvel at the difference.

Your code will first be measured for correctness, ensuring that it analyzes input
files correctly.

If you pass the correctness tests, your code will be tested for performance;
higher performance will lead to better scores.





