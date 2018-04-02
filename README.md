//untuk menjalankan --> gcc -pthread -o tugasSO tugasSO.c
// ./tugasSO
// jalankan pstree -p , caripada bagian terminal.
// agar lebih singkat
// pstree PID_terminal -p

#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/sem.h>
#include <semaphore.h>
#include <pthread.h>

sem_t mutex;

void* C2(void* arg)
{
    //wait
    sem_wait(&mutex);
	printf("Hallo saya cucu nya Maruf, PID bapak saya %d.\n", getppid());
    //critical section
    sleep(3);
    //signal
    sem_post(&mutex);
    return 0;
}

int main(int argc, char **argv)
{
	pid_t p1,firstChild, secondChild;
	
	p1 = getpid();
    printf("Hi. saya Maruf PID saya %d.\n", p1);
    
	firstChild = fork();
	if(firstChild > 0)
	{
	  // In parent
	  secondChild = fork();
	  if(secondChild > 0){
		// In parent
	  }
	  else if(secondChild < 0){
		// Error
	  }
	  else{
		printf("Hallo saya anak kedua Maruf, PID bapak saya %d. PID saya %d.\n", getppid(), getpid());
	  }
	}
	else if(firstChild < 0 ){// Error
	} 
	else{
		printf("Hallo saya anak pertama Maruf, PID bapak saya %d. PID saya %d.\n", getppid(), getpid());
		sem_init(&mutex, 0, 1);
		pthread_t t1,t2, t3;
		pthread_create(&t1,NULL,C2,NULL);
		sleep(1);
		pthread_create(&t2,NULL,C2,NULL);
		sleep(1);
		pthread_create(&t3,NULL,C2,NULL);
		sleep(1);
		pthread_join(t1,NULL);
		pthread_join(t2,NULL);
		pthread_join(t3,NULL);
		sem_destroy(&mutex);
	}
	sleep(15);
	return 0;
}
