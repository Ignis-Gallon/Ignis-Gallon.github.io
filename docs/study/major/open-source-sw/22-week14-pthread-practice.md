---
title: "14주차: pthread 실습"
date: 2022-12-01
last_modified_at: 2022-12-04
category: major
tags: [오픈소스SW설계, 쓰레드, pthread]
---

# 14주차: pthread 실습

작성일 2022-12-01 · 최종 수정일 2022-12-04
{ .post-date }

Q. 프로세스와 쓰레드의 차이점은?  
A. 프로세스를 새롭게 만드는 것은 fork()를 해서 overhead가 크지만, 쓰레드는 stack과 register 값만 복사하면 되기 때문에 상대적으로 빠르다. 또한, 데이터 영역을 공유하기 때문에 쓰레드 간의 데이터 공유가 쉽다. (프로세스 간의 통신은 쉽지 않다. ls | wc -l 과 같은 방식이 프로세스 간의 통신)

- pthread_create : thread를 만드는 API. 원래는 없었지만, 나중에 POSIX-thread에서 표준화되면서 만들어졌다.

int pthread_create();

- pthread_t \*thread : 쓰레드 ID
- const pthread_attr_t \*attr : 여기서는 NULL로 하지만, 원래는 우선순위나 스케쥴링과 관련해서 attribute 설정을 많이 해준다.
- void\*(\*start_routine) (void \*) : 실행할 함수의 function pointer
- void \*arg : 함수에 전달해줄 인자.

> gcc하는 과정에 -pthread 라고 입력해서 링크해야함. (-lpthread 아님)  
> 따로 설정된 옵션이므로, 앞으로는 이걸 이용하도록 한다.

Q. pthread_create()를 이용해 myfunc() 함수를 수행하는 쓰레드를 생성하라.

- my code

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

void* myfunc(void *arg) {
    printf("Hello, World!\n");
    pthread_exit(NULL);
}

int main(void) {

    pthread_t mythread;
    int result;
    result = pthread_create(&mythread, NULL, myfunc, NULL);

    if (result) {
        printf("error occurred!\n");
        exit(1);
    }

    pthread_join(mythread, NULL);
    printf("I am the main thread!\n");
}
```

- professor’s code

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void* myfunc(void *arg) {
    printf("Hello, World!\n");
    pthread_exit(NULL);
}

int main(void) {

    pthread_t mythread;
    int result;
    result = pthread_create(&mythread, NULL, myfunc, NULL);
    // 원래 thread를 많이 만드는 프로그램은
    // 반드시 error code checking을 해주어야 한다.
    // 한 프로세스에 할당된 메모리의 크기는 유한하므로,
    // 게속해서 thread를 만들 수 없다.

    if (result) {
        printf("error occurred!\n");
        exit(1);
    }

    // join이 꼭 필요한가?
    // join을 꼭 해야한다.
    // pthread_join(mythread, NULL);
    
    sleep(1);
    printf("I am the main thread!\n");
}
```

- pthread_join을 안하면 기다려주지 않으므로, 다른 thread의 실행을 기다려주어야 한다. sleep() 함수를 사용하면 동작은 되지만, 이것은 비효율적인 동작. 따라서, 멀티 프로세스 환경의 wait()과 비슷한 동작을 하는 pthread_join()을 추가해준다.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void* myfunc(void *arg) {
    printf("Hello, World!\n");
    pthread_exit(NULL);
}

int main(void) {

    pthread_t mythread;
    int result;
    result = pthread_create(&mythread, NULL, myfunc, NULL);
    // 원래 thread를 많이 만드는 프로그램은
    // 반드시 error code checking을 해주어야 한다.
    // 한 프로세스에 할당된 메모리의 크기는 유한하므로,
    // 게속해서 thread를 만들 수 없다.

    if (result) {
        printf("error occurred!\n");
        exit(1);
    }
    
    printf("I am the main thread!\n");
	
    // join이 꼭 필요한가?
    // join을 꼭 해야한다.
		// return하는 값에 관심이 없으면 NULL로 해준다.
    pthread_join(mythread, NULL);
}
```

- pthread_join은 main thread가 끝나기 전에 걸어주면, thread의 실행을 기다릴 수 있다.
- thread 100개 만들어보기

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

// refactoring, 코드를 개선하는 과정.
#define NUM_THREAD 100

void* myfunc(void *arg) {

    printf("thread number\n");
    pthread_exit(NULL);
}

int main(void) {

    pthread_t mythread[NUM_THREAD];

    for (int i = 0; i < NUM_THREAD; i++) {

        int result;
        result = pthread_create(&mythread[i], NULL, myfunc, NULL);

        if (result) {
            printf("error occurred!\n");
            exit(1);
        }
    }

    printf("I am the main thread!\n");

    for (int i = 0; i < NUM_THREAD; i++) {
        
        pthread_join(mythread[i], NULL);
    }
}
```

- argument를 줘도 되지만, ./a.out | wc -l을 사용하면 출력된 라인 체크 가능.
- pthread_join은 인자를 pointer로 받지 않는다.
- 두 개가 동시에 접근할 수 있는 데이터를 이용해 데이터 통신을 시도해보자.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

// refactoring, 코드를 개선하는 과정.
#define NUM_THREAD 1

static long long counter = 0;
static char is_running = 1;

void* myfunc(void *arg) {

    while (is_running == 1) {
        counter++;
    }

    pthread_exit(0);
}

int main(void) {

    pthread_t mythread[NUM_THREAD];
    int sleep_counter = 0;

    for (int i = 0; i < NUM_THREAD; i++) {

        int result;
        result = pthread_create(&mythread[i], NULL, myfunc, NULL);

        if (result) {
            printf("error occurred!\n");
            exit(1);
        }
    }

    // for문으로 짜도 됨.
    while (sleep_counter < 10) {
        
        printf("COUNTER : %lld\n", counter);
        sleep(1);
        sleep_counter++;
    }

    is_running = 0;

    for (int i = 0; i < NUM_THREAD; i++) {
        pthread_join(mythread[i], NULL);
    }

    printf("Program Ended!\n");
}
```

- gcc mythread.c -pthread -O2 를 하면, 코드를 optimization할 수 있다.

그런데, 코드를 동작시키면 counter가 계속 0으로 나온다. 이는 멀티 코어로 동작시키고 있기 때문이다.

counter, is_running 변수는 둘 다 memory 상에 존재한다. main thread가 변수값을 변경하면 new thread에서 그 값을 확인하고, 루프를 종료하는 구조이다. 컴퓨터에서 가장 느린 operation은 disk에서 값을 가져오는 과정이다. 그러므로 값을 cache에 갖다 놓거나, register에 값을 넣어놓는다. 이것은 main thread, new thread가 cache를 각각 가지게 되므로, 각각 cache에 존재하는 변수를 바꿔주는 거라서 올바른 동작이 안된 것이다.

프로그램의 최적화는 느린 작업을 빠른 작업으로 교체해주는 과정이다. 특히 memory accessing이 가장 느리기 때문에 이 과정을 바꿔준 것이다. L1 cache는 core 안에 있고, L2 cache는 메모리에 존재하는데, 이번 예제에서는 L1 cache를 사용했기 때문에 서로 통신이 불가능했던 것이다.

그렇다면 어떻게 할 것인가? cache값은 우리가 조절할 수 없다. cache는 숨어있는 장소라서, 우리에게 안보이기 때문에 컨트롤할 수 없다. cache에 있는 내용이 메모리에 다시 저장하는 경우는, 오직 cache를 써야하는데 cache 공간이 부족할 때 뿐이다. 정확히 cache를 언제 쓸지는 알 수 없다.

⇒ 그래서, 최적화에서 제외하는 옵션을 줘야한다. volatile

- volatile : memory access를 계속하는 값일 수 있기 때문에, 저장되는 위치를 바꾸거나 하는 걸 하면 안된다는 뜻. (multi-threading 환경에서 volatile이 중요하게 사용됨.)
- thread에 argument 넘겨주기 : 보통 쓰레드 번호를 넘겨주거나, 처리할 데이터의 범위를 넘겨주는 걸 많이 한다.

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

// refactoring, 코드를 개선하는 과정.
#define NUM_THREAD 1

struct thread_data {
    long long start;
    long long end;
    long long sum;
};

void* worker(void* arg) {

    struct thread_data* p = (struct thread_data*) arg;
    printf("%lld %lld\n", p->start, p->end);

    for (long long i = p->start; i <= p->end; i++) {
        p->sum += i;
    }

    printf("%lld\n", p->sum);
    pthread_exit(0);
}

int main(void) {

    pthread_t mythread;
    struct thread_data my_data;
    my_data.start = 0;
    my_data.end = 10;
    my_data.sum = 0;

    int result;
    // 뭘 넘겨줄지 모르기 때문에, void pointer를 넘겨준다.
    result = pthread_create(&mythread, NULL, worker, (void*)&my_data);
    // 포인터로 넘겨줘서 포인터로 값을 바꾸므로,
    // 원본의 데이터가 바뀐다...

    if (result) {
    printf("error occurred!\n");
        exit(1);
    }

    pthread_join(mythread, NULL);
    // pthread_join은 exit로 exit number를 받는다.

    // pthread_join이 return을 void** type으로 주므로,
    // (void**) type으로 casting해서 받아야 한다.

    printf("%lld\n", my_data.sum);
    printf("Program Ended!\n");
}
```

- 과제에 사용할 utility(thread의 개수를 1,2,4,8로 바꾸면서 1,000,000,000 을 쓰레드에서 더하기)

```shell
time ./a.out
```
