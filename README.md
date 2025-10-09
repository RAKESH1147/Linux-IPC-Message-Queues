# Linux-IPC-Message-Queues
Linux IPC-Message Queues

# AIM:
To write a C program that receives a message from message queue and display them

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux message queues API 

### Step 3:

Execute the C Program for the desired output. 
Developed by : PRIYANGA R Register Number : 212223230161
# PROGRAM:
```
/*
 * sem-producer-consumer.c  - demonstrates a basic producer-consumer
 *                            implementation.
 */
#include <stdio.h>	 /* standard I/O routines.              */
#include <stdlib.h>      /* rand() and srand() functions        */
#include <unistd.h>	 /* fork(), etc.                        */
#include <time.h>	 /* nanosleep(), etc.                   */
#include <sys/types.h>   /* various type definitions.           */
#include <sys/ipc.h>     /* general SysV IPC structures         */
#include <sys/sem.h>	 /* semaphore functions and structs.    */
#define NUM_LOOPS	20	 /* number of loops to perform. */
#if defined(__GNU_LIBRARY__) && !defined(_SEM_SEMUN_UNDEFINED)
/* union semun is defined by including <sys/sem.h> */
#else
/* according to X/OPEN we have to define it ourselves */
union semun {
        int val;                    /* value for SETVAL */
        struct semid_ds *buf;       /* buffer for IPC_STAT, IPC_SET */
        unsigned short int *array;  /* array for GETALL, SETALL */
        struct seminfo *__buf;      /* buffer for IPC_INFO */
};
#endif
int main(int argc, char* argv[])
{
    int sem_set_id;	      /* ID of the semaphore set.       */
    union semun sem_val;      /* semaphore value, for semctl(). */
    int child_pid;	      /* PID of our child process.      */
    int i;		      /* counter for loop operation.    */
    struct sembuf sem_op;     /* structure for semaphore ops.   */
    int rc;		      /* return value of system calls.  */
    struct timespec delay;    /* used for wasting time.         */
	/* create a private semaphore set with one semaphore in it, */
    /* with access only to the owner.                           */
    sem_set_id = semget(IPC_PRIVATE, 1, 0600);
    if (sem_set_id == -1) {
	   perror("main: semget");
	   exit(1);
    }
    printf("semaphore set created, semaphore set id '%d'.\n", sem_set_id);
    /* intialize the first (and single) semaphore in our set to '0'. */
    sem_val.val = 0;
    rc = semctl(sem_set_id, 0, SETVAL, sem_val);
    /* fork-off a child process, and start a producer/consumer job. */
    child_pid = fork();
    switch (child_pid) {
	  case -1:	/* fork() failed */
	    perror("fork");
	    exit(1);
	 case 0:		/* child process here */
	    for (i=0; i<NUM_LOOPS; i++) {
		/* block on the semaphore, unless it's value is non-negative. */
		sem_op.sem_num = 0;
		sem_op.sem_op = -1;
		sem_op.sem_flg = 0;
		semop(sem_set_id, &sem_op, 1);
		printf("consumer: '%d'\n", i);
		fflush(stdout);
	    }
	    break;
		default:	/* parent process here */
			for (i=0; i<NUM_LOOPS; i++) {
			printf("producer: '%d'\n", i);
			fflush(stdout);
			/* increase the value of the semaphore by 1. */
			sem_op.sem_num = 0;
	sem_op.sem_op = 1;
			sem_op.sem_flg = 0;
			semop(sem_set_id, &sem_op, 1);
		/* pause execution for a little bit, to allow the */
		/* child process to run and handle some requests. */
		/* this is done about 25% of the time.            */
		if (rand() > 3*(RAND_MAX/4)) {
	    	    delay.tv_sec = 0;
	    	    delay.tv_nsec = 10;
	    	    //nanosleep(&delay, NULL);
		                      sleep(10); }
			
if(NUM_LOOPS>=10)    {
	    semctl(sem_set_id, 0, IPC_RMID, sem_val) ; // Remove the sem_set_id
	    
	    break;

		}
	}}
    
    return 0;
}
```
## C program that receives a message from message queue and display them

```
// C Program for Message Queue (Reader Process)
#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>

// structure for message queue
struct mesg_buffer {
	long mesg_type;
	char mesg_text[100];
} message;
int main()
{
	key_t key;
	int msgid;
    	// ftok to generate unique key
	key = ftok("progfile", 65);
	// msgget creates a message queue
	// and returns identifier
	msgid = msgget(key, 0666 | IPC_CREAT);
	// msgrcv to receive message
	msgrcv(msgid, &message, sizeof(message), 1, 0);
	// display the message
	printf("Data Received is : %s \n",
			message.mesg_text);

	// to destroy the message queue
	msgctl(msgid, IPC_RMID, NULL);
	return 0;
}

```
## OUTPUT
$ ./sem.o
![image](https://github.com/Priyangarajapandiyan/Linux-IPC-Message-Queues/assets/144872535/af9c501d-a6b8-488e-80d6-4a6ebf552e62)

$ ipcs
![image](https://github.com/Priyangarajapandiyan/Linux-IPC-Message-Queues/assets/144872535/1c7f55cb-f6df-40b9-85a9-1c4e39f53037)


# RESULT:
The programs are executed successfully.
