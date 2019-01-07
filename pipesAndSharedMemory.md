# Pipes and Shared Memory
As we have learned, each process has its own space in memory. If we call `fork` the child process gets an identical but separate copy of the contents of this memory including the text, data, and bss segments, heap and stack. So even a child process does not share memory with its parent process. Consider the following example:

   #include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <string.h>
	#include <sys/types.h>
	#include <sys/wait.h>


	int main(){
		pid_t pid;
		int x = 1;
		printf("Value of x before fork: %i \n", x);

		pid = fork();

		if (pid < 0){
			fprintf(stderr, "Fork Failed");
		}
		else if (pid > 0){
			/* parent process */

			/* now wait for child  */
			wait(NULL);

			/* now print x */
		    printf("Value of x in parent: %i \n", x);


		}
		else { /* child process */
			x = x + 1;
			printf("Value of x in child: %i \n", x);      
		}
		exit (0);
	}

Please take your time and review the code.  What do you think will be printed as a result of:

	printf("Value of x in parent: %i \n", x);

Here are the results:

	./noSharedMemory
	Value of x before fork: 1 
	Value of x in child: 2 
	Value of x in parent: 1 

You can see that updating a variable in a child process has no impact on that variable in the parent process. Why? Because a child process makes a copy of the memory of the parent.

When we fork a process, we want some work to be done and often that requires that the child process shares information with its parent. There are two good ways to do this. One method uses pipes and the other shared memory.


## Pipes
Unix pipes provide a one way communication channel between two processes. Hopefully you are already familiar with using pipes on the command line. An example of one use of pipes that I use frequently is something like:

	$ ps -ef | grep python
	root       613     1  0 Jan03 ?        00:00:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
	vagrant  13696  1174  0 21:12 pts/0    00:00:00 grep --color=auto python

So we can pipe the output of `ps -ef` into `grep`.

Let's take a look as to how we can use pipes in our C code.

### Pipes in C.

To create a simple pipe with C, we make use of the `pipe() `system call. It takes a single argument, which is an array of two integers, and if successful, the array will contain two new file descriptors to be used for the pipeline. After creating a pipe, the process typically spawns a new process (remember the child inherits open file descriptors).

  	SYSTEM CALL: pipe();                                                          

  	PROTOTYPE: int pipe( int fd[2] );                                             
    	RETURNS: 0 on success                                                       
          	   -1 on error: errno = EMFILE (no free descriptors)                  
                                  EMFILE (system file table is full)            
                                  EFAULT (fd array is not valid)                

  	NOTES: fd[0] is set up for reading, fd[1] is set up for writing

The first integer in the array (element 0) is set up and opened for reading, while the second integer (element 1) is set up and opened for writing. All data traveling through the pipe moves through the kernel.  These 2 integers are file descriptors. In Unix and related computer operating systems, a file descriptor, or **fd** is an abstract indicator (handle) that is used to access a file or other input/output resource, such as a pipe or network socket. So `pipe()` sets the array argument to 2 file descriptors. The first is for the read end of the pipe; the second for the write end.

The following example is not the most compelling example of why you would want to use pipes--in fact it is a bit of a silly use--but it does offer a clear example of how to use pipes.

Suppose we want to do this.

	fork
	the parent process gets some text input from the user
	the parent process sends that text to the child using a pipe
	the child process receives that information from the pipe.
	the child process appends the word 'intelligence' to the end of the input
	the child process sends that new text to the parent using a pipe
	the parent receives that string and prints it.
	
Recall that pipes are one way communication channels so we cannot use the same pipe to send information from parent to child and to send information from child to parent. So we need two pipes. Let's call them `fd1` and `fd2`. 

With this introduction you should be able to understand the following code.

	#include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <string.h>
	#include <sys/types.h>
	#include <sys/wait.h>


	int main(){
		int fd1[2]; /* both ends of first pipe */
		int fd2[2]; /* both ends of second pipe */

		char suffix_string[] = " intelligence";
		char input_string[100];

		pid_t pid;

		if ((pipe(fd1) < 0) || (pipe(fd2) < 0)){
			fprintf(stderr, "Pipe Failed");
			return 1;
		}
		pid = fork();

		if (pid < 0){
			fprintf(stderr, "Fork Failed");
		}
		else if (pid > 0){
			/* parent process */
	        scanf("%s", input_string);
	 		char final_string[100];

			close(fd1[0]); /* close reading end of pipe 1 */
			write(fd1[1], input_string, strlen(input_string) + 1);
			close(fd1[1]);

			/* now wait for child to write to pipe */
			wait(NULL);
			close(fd2[1]);  /* close writing end of pipe 2 */
			read(fd2[0], final_string, 100);
			close(fd2[0]);

			/* NOW PRINT RESULT */
			printf("\n%s \n", final_string);


		}
		else { /* child process */

		    char concatenated_string[100];
			close(fd1[1]); /* close writing end of pipe 1 */
			read(fd1[0], concatenated_string, 100);

			/* concatenate the strings */
			int k = strlen(concatenated_string);
			for (int i = 0; i < strlen(suffix_string); i++){
				printf(".");
				concatenated_string[k++] = suffix_string[i];
			}
			concatenated_string[k] = '\0'; /* string terminator */
			close(fd1[0]); /* close reading end of pipe 1 */
			close(fd2[0]); /* close reading end of pipe 2 */

			/* write final string to writing end of pipe 2 */
			write(fd2[1], concatenated_string, strlen(concatenated_string) + 1);
			close(fd2[1]);
	        
		}
		exit (0);
	}


## Shared Memory
I like pipes because they are a clean way of sending information between processes. But another way would be to use shared memory. Shared memory allows two or more processes to share the same space in memory. It is faster than pipes because information does not need to be copied between the communicating processes. As we will explore later in the class, the problem with shared memory is synchronization. It is possible that one process will overwrite some data from another process.  Let's see how shared memory works.

There are several shared memory methods. Back in the day -- way, way. back when the Unix operating system developed, there were two competing major distributions of Unix. One was System V ("System Five") and the other was BSD (Berkeley Software Distribution). Both of these Unix OSs developed implementations for shared memory that are still used today. Let's examine the System V implementation. 

** System V Shared Memory**

The two functions that are used are `shmget()` and `shmat()`
Let us go through a simple example:

Sample:

	#include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <string.h>
	#include <ctype.h>
	#include <signal.h>
	#include <errno.h>
	#include <sys/ipc.h>
	#include <sys/shm.h>
	#include <sys/wait.h>
	#include <sys/types.h>
	#include <sys/ipc.h>
	#include <sys/shm.h>
	int *result;

	inline static void 
	unix_error(char *msg)
	{
	    fprintf(stdout, "%s: %s\n", msg, strerror(errno));
	    exit(1);
	}

	pid_t Fork(void) {
	  pid_t pid;
	  if ((pid = fork()) < 0) 
	    unix_error("Fork Error");
	  return pid;
	}

	int main(){
		int shmid;
		int status;
		if ((shmid = shmget(IPC_PRIVATE, sizeof(int), SHM_R | SHM_W)) < 0) {
			unix_error("ERROR SHMGET");
		}
		if ((result = (int *)shmat(shmid, NULL, 0)) == (int *) -1) {
	        unix_error("ERROR SHMAT");
		}

		pid_t pid;
	    
	    pid = Fork();
	    if (pid == 0){  /* child */
	    	*result = 143;
		}
		else{  /* parent */
		    wait(&status);
		    printf("Mr. Roger's favorite number: %i \n", *result );
		}
		
		}
		
### shmget()

In our sample program we used:

	shmid = shmget(IPC_PRIVATE, sizeof(int), SHM_R | SHM_W)
	
the template is:

	id = shmget(key_t key, int size, int flag)	
	
This is used to get a shared memory identifier. It returns an id if successful otherwise it returns -1. 

* `key` is typically `IPC_PRIVATE` which let's the kernel decide on a new key. Keys are system-wide.
* `size` is the size of the shared memory segment in bytes.
* `flag` are the permissions on the segment which can be `SHM_R`, `SHM_W` or `SHM_R | SHM_W`.

Once we have created a shared memory space, the process attaches it to its address space using

### shmat()
In our sample program we used:

	result = (int *)shmat(shmid, NULL, 0)
	
the template is:

	pointer = void *shmat(shmid, void* addr, int flag)
	
	
If successful, shmat will return a pointer to the shared memory segment else it will return -1.

It is standard practice to set the `addr` to `NULL` and the `flag` to `0`.	


		 
		   
## The assignment - Fibonacci

The task is to modify the template [fib.c](https://github.com/zacharski/cpsc405/blob/master/source/fib.c) so that if invoked on the command line with some integer argument `n`  it recursively computes the nth Fibonacci number (n ≤ 13).
					
e.g.,
					
	unix> fib 3
	2
	unix> fib 10
	55


					
The trick is that each recursive call must be made by a new process, so you will call `fork()` and then have the new child process call `doFib()`.
					
The parent must wait for the child to complete and you need to figure out how to pass the result of the child’s computation to its parent. You will write and submit two versions of the program. One that passes the result using shared memory, the other using pipes.

