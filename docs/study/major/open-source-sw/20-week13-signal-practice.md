---
title: "13주차: 시그널의 종류와 실습"
date: 2022-11-24
last_modified_at: 2022-11-24
category: major
tags: [오픈소스SW설계, 시그널, sigaction]
---

# 13주차: 시그널의 종류와 실습

작성일 2022-11-24
{ .post-date }

- signal이란 무엇인가?  
    → man 7 signal 을 해서 section 7을 보면, signal에 대한 overview. 전반적인 설명이 나온다. 사실 signal은 숫자로 선언되어 있는데, signal.h에 이름으로 define되어 있다.

시그널은 외부에서 발생하는 이벤트를 프로세스에 알려주는 것이므로, Asynchronous하게 동작하는 S/W적인 Interrupt이다. 그래서 언제, 어떤 것이 발생하게 될 지 알 수 없다. 그래서 시그널 프로그래밍을 하는 방법은 그 시그널이 처리할 방법(signal handler)을 지정해두고, 시그널이 도착하면 본래 수행되던 프로세스를 중지했다가 handler의 수행이 끝나면 다시 프로세스로 돌아오게 된다.

H/W 레벨에서는 clock에 의해서 모든 것이 Synchronous하게 동작하기 때문에 컴퓨터는 H/W 자체적으로는 Synchronous System이다. 하지만, OS가 부착되어 추상화(abstraction)가 되면서 시간(clock)이라는 개념을 가려버리기 때문에 컴퓨터에서 동작하는 S/W는 Asynchronous하게 동작하는 것이 대부분이다.

이 시간(clock)이라는 개념이 사라지면서 프로그램이 쉬워진다고 생각할 수 있지만, 나중에 multi process, multi thread를 도입하게 되면 어려움이 발생할 수 있다. 예를 들면, 스케쥴러가 어떻게 설정하냐에 따라서 thread가 어떤 순서로 돌지가 결정되기 때문에, thread의 동작 순서를 예측하고 프로그램을 작성할 수 없다.

---

- default action : 현재 프로세스 내에 설정된 register 값이나 memory의 값을 .swp라는 확장자의 파일로 저장하고 프로그램을 종료한다.

OS가 알려주는 signal도 많은데, SIGFPE, SIGBUS 등등이 있다. 이와 다르게 우리가 manual하게 명시적으로 발생시키는 signal도 있다. SIGINT(Ctrl+C), SIGCHLD(child process stopped) 등등이 있다. 즉, 누가 signal을 발생시키는 가는 정해져 있는게 아니라 다양하다.

- 그 중 특히 우리가 많이 쓰는 것은 SIGKILL → 모든 signal은 handler를 가지지만, SIGKILL은 handler를 설정할 수 없다(SIGSTOP도 handler 설정 불가능). 이와 비슷하게 SIGTERM(종료)가 있지만, 이것은 handler를 설정할 수 있다.
- 만약 리눅스 환경에서 C로 포인터를 잘못 사용한다면, SIGSEGV(잘못된 메모리 참조) signal을 만나게 될 수도 있다.

---

Q. 1 : 시그널 실습을 위해 무한 루프 프로그램을 작성해보자.

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

int main(void) {

    while(1) {
        printf("pid : %d\n", getpid());
        sleep(1);
    }

    return 0;
}
```

- 프로세스에 줄 수 있는 가장 간단한 시그널

SIGINT(Ctrl + C) : term  
SIGSTOP(Ctrl + Z) : stop  
SIGQUIT(Ctrl + \\) : term

- kill : 특정 프로세스에게 시그널을 보내주는 시스템 콜이다. 만약 위에서 작성한 프로세스에 SIGINT signal을 보내고 싶다면, 다음과 같이 shell에 보낸다.

ex)

kill -SIGINT pid

kill -SIGSTOP pid

kill -SIGCONT pid

kill -SIGSTOP pid

⇒ 이걸 하면 터미널에서 떨어져나가므로, 터미널에서 Ctrl+C를 하면 안먹는다.  
SIGSTOP을 하면, Running → Suspended로 간다. 그 이후에 SIGCONT를 하면, Suspended → Runnable로 간다. 그러면 다시 실행될 수 있는 상태가 된다.

---

- GDB : GNU Debugger

디버깅을 해보자!

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

void foo() {
    printf("hello foo\n");
}

void goo() {
    printf("hello goo\n");
    foo();
}

int main(void) {

    while(1) {
        goo();
        printf("pid : %d\n", getpid());
        sleep(1);
    }

    return 0;
}
```

→ 디버깅을 어떻게 하느냐? 컴파일 할 떄 -g 옵션을 주어야 한다.  
gcc signal1.c -g -o signal1 (디버깅을 위한 컴파일)  
gcc signal1.c -o signal1_release (일반 컴파일)

두 파일이 동작은 완전히 똑같다. 그런데, 용량을 보면 signal1(디버깅용)의 사이즈가 더 크다.  그 이유는 디버깅을 할려면 라인 위치에 대한 매핑 등등 정보가 더 필요하기 때문에 용량이 더 큰데, 그래서 실제로 출시할 때는 release mode로 한다. (디버깅 정보 제거)

gdb ./ (break point 설정, step, bt 사용)

goo()가 실행된 결과는 나오는데, goo() 안으로 들어가고 싶을 때가 있을 것이다. 함수를 호출하고 그냥 넘어가지 말고, 그 안으로 들어가려면 step이라는 명령어를 써야 한다. 그냥 s라고만 입력하면 step으로 들어간다. ( goo()가 화면 line에 떴을 때 )

print var_name을 하면 변수명도 출력이 가능하다.

나중에 segmentation fault를 처리할 때 중요한 것이 back trace이다. bt또는 backtrace라고 검색하면 된다. bt를 하면 stack을 표현해준다. main에서 goo()를 부르고 foo()를 부른다. foo()가 끝나면 goo()로 돌아가고, goo()가 끝나면 main()으로 돌아간다. 어디로 돌아가야 되는지를 알려주는 것이 stack이다.

---

- 무한 루프 프로그램이 signal을 받으면, ouch!를 출력하도록 핸들러를 설정하라.

man 2 signal : signal()은 사용하지 말라는 내용 있음…

사실 UNIX가 multi process는 옛날부터 됐는데, multi thread가 적용되기 시작한 것은 30년도 채 안됐다. signal()은 multi process가 나오기 전에 생겼으므로, 에러가 많이 발생한다. 따라서 앞으로는 무조건 sigaction(2)을 사용한다.

- sigaction : signal handler를 등록하는 일을 한다.

int sigaction(int signum, const struct sigaction \*act, struct sigaction \*oldact);  
여기서 중요한 것은, handler를 sigaction struct 내에서 선언해주어야 한다. 여기는 handler를 function pointer 형태로 전달 받는데, 사실 C언어에서 function은 그냥 명령어들이 수행되는 집합의 첫 주소를 나타내는 이름이다. 그래서 function의 주소를 얻고 싶으면 function name을 주면 된다.

sigset_t sa_mask;는 내가 signal을 수행하고 있을 때 막고 싶은 signal을 기록해두는 곳이다.

sa_flags는 보통은 0으로 표현한다.

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

void my_int_handler(int signo) {

    printf("ouch!, sig : %d\n", signo);

}

int main(void) {

    // sigaction을 생성할 때는 구조체이므로,
    // struct sigaction으로 type을 설정한다.
    struct sigaction sa;

    // sigset 함수들은 argument를 주소로 전달받는다.
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sa.sa_handler = my_int_handler;

    // 만약, 기존에 설정되어 있던 sa_mask를 기록하고 싶다면
    // struct sigset type 변수를 만들어서 주소를 넘겨준다.
    sigaction(SIGINT, &sa, NULL);
    while(1) {
        printf("loop... pid : %d\n", getpid());
        sleep(1);
    }

    return 0;
}
```

sigaction안에 있는 sa_mask는 비트 단위로 조작해야돼서 다루기가 쉽지 않으므로 사전에 define된 macro를 사용한다.

ex) sigemptyset(\*) 주소를 받으므로, ampersand를 써준다.

---

- 이번엔 SIGQUIT에 대해서도 handler를 추가해주자.

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

void my_int_handler(int signo) {

    printf("ouch!, signo : %d\n", signo);
}

void sigquit_handler(int signo) {

    printf("Goodbye!, signo : %d\n", signo);
    exit(0);
}

int main(void) {

    // sigaction을 생성할 때는 구조체이므로,
    // struct sigaction으로 type을 설정한다.
    struct sigaction sa;

    // sigset 함수들은 argument를 주소로 전달받는다.
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sa.sa_handler = my_int_handler;

    // 만약, 기존에 설정되어 있던 sa_mask를 기록하고 싶다면
    // struct sigset type 변수를 만들어서 주소를 넘겨준다.
    sigaction(SIGINT, &sa, NULL);

    sa.sa_handler = sigquit_handler;
    sigaction(SIGQUIT, &sa, NULL);

    while(1) {
        printf("loop... pid : %d\n", getpid());
        sleep(1);
    }

    return 0;
}
```

handler를 설정해서 SIGQUIT signal이 process에 전달되면 Goodbye!를 출력하고 종료(exit(0))한다.  
SIGQUIT : Ctrl+\\

- professor’s code

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

void my_int_handler(int signo) {

    printf("ouch!, signo : %d\n", signo);
}

void sigquit_handler(int signo) {

    printf("Goodbye!, signo : %d\n", signo);
    exit(0);
}

int main(void) {

    // sigaction을 생성할 때는 구조체이므로,
    // struct sigaction으로 type을 설정한다.
    struct sigaction sa_INT, sa_QUIT;

    // sigset 함수들은 argument를 주소로 전달받는다.
    sigemptyset(&sa_INT.sa_mask);
    sa_INT.sa_flags = 0;
    sa_INT.sa_handler = my_int_handler;

    // 만약, 기존에 설정되어 있던 sa_mask를 기록하고 싶다면
    // struct sigset type 변수를 만들어서 주소를 넘겨준다.
    sigaction(SIGINT, &sa_QUIT, NULL);

    sigemptyset(&sa_QUIT.sa_mask);
    sa_QUIT.sa_handler = sigquit_handler;
    sigaction(SIGQUIT, &sa_QUIT, NULL);

    while(1) {
        printf("loop... pid : %d\n", getpid());
        sleep(1);
    }

    return 0;
}
```

---

- alarm & timer

alarm() 시스템 콜을 설정해두면, 지정한 초만큼 시간이 지나면 SIGALRM signal이 온다. 이번 예제에서는 알람이 도착하면(SIGALRM) “Alarm arrived!”를 출력해주고, 매 1초마다 SIGALRM을 발생시키도록 한다.

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>

void my_int_handler(int signo) {

    printf("ouch!, signo : %d\n", signo);
}

void sigquit_handler(int signo) {

    printf("Goodbye!, signo : %d\n", signo);
}

void alarm_handler(int signo) {

    printf("Alarm arrived!\n");
    alarm(1);
}

int main(void) {

    // sigaction을 생성할 때는 구조체이므로,
    // struct sigaction으로 type을 설정한다.
    struct sigaction sa;

    // sigset 함수들은 argument를 주소로 전달받는다.
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sa.sa_handler = my_int_handler;

    // 만약, 기존에 설정되어 있던 sa_mask를 기록하고 싶다면
    // struct sigset type 변수를 만들어서 주소를 넘겨준다.
    sigaction(SIGINT, &sa, NULL);

    sa.sa_handler = sigquit_handler;
    sigaction(SIGQUIT, &sa, NULL);

    sa.sa_handler = alarm_handler;
    sigaction(SIGALRM, &sa, NULL);

    alarm(1);

    while(1) {
        printf("loop... pid : %d\n", getpid());
        sleep(1);
    }

    return 0;
}
```

> signo parameter를 전달받는 것은, 비슷한 signal은 하나의 handler 안에서 처리할 수 있도록 처리를 하기 위해 활용할 수 있다.

알람을 여러 개 설정할 수도 있다. (POSIX real-time clock)

---

C에서는 한 줄인 코드들도 사실 machine language로 보면 한 줄이 아니다. 그렇기 때문에, 값을 넣고 있는 중간에 값이 들어오면 값이 채 다 바뀌기 전에 시그널이 오면 값이 다 바뀌지 않고 동작이 수행될 수 있다. → sigprocmask를 사용해서 0과 1만 출력되도록 만든다.

⇒ mask로 block이 되어 있어도 날라온 SIGNAL이 버려지는게 아니기 때문에, 미세한 차이는 존재할 수 있으나 육안으로는 큰 차이가 없을 것이다.
