---
title: "11주차: 시스템 콜과 파일 입출력"
date: 2022-11-10
last_modified_at: 2022-12-03
category: major
tags: [오픈소스SW설계, 시스템 콜, 파일 입출력, errno]
---

# 11주차: 시스템 콜과 파일 입출력

## 시스템 콜과 파일 입출력

OS 안에서의 시스템 콜이란?

> The system call is the fundamental interface between an application and the Linux kernel.
>
> 컴퓨터에서 인터페이스라고 한다면, GUI를 제외하고는 대부분 함수의 형태로 주어진다.

현재 리눅스 커널을 배포하는 사이트는 kernel.org이며, 현재를 기준으로 리눅스 최신 버전은 6.0.7  
⇒ 몇 개의 system call이 있는지 확인하려면 /usr/include/x86_64-linux-gnu/asm/unistd.h 를 확인해본다. 내용을 살펴보면, 또 다시 ifndef가 되어있는데, 대부분의 컴퓨터 환경은 64bit를 사용하므로 …/asm/unistd_64.h를 살펴보자. 시스템 콜은 실제로는 이름이 없고 번호만 부여되어 있지만, 이러면 프로그래밍이 힘드므로 refer를 붙여준다. (이것을 우리는 이름으로 부름)

시스템 콜은 파일 입출력/프로세스 관리/시그널 등등의 역할을 한다.

1. getpid

    man getpid를 하면, 설명이 나오게 된다. 그런데 우리가 여기서 확인해봐야 하는 것은, ERROR가 발생하는 경우를 확인해야 한다. 예를 들어 우리가 파일이나 디렉터리를 만들 때 실패할 경우가 생각보다 많은데, 이를 잘 처리해주지 않으면 시스템이 망가질 수도 있다.

    CONFORMING TO 를 확인하면, 이 system call을 이용하여 프로그래밍 한 것을 해당 compile 버전에서 컴파일할 수 있게 된다.

    가끔씩 pid_t라는 type의 return type이 존재하는데, 이 것은 unsigned int라고 생각하면 된다. unsigned int가 어떤 플랫폼에서는 32bit이고 어떤 플랫폼에서는 64bit인데 그 차이를 감추기 위해 type을 따로 만들어준다. 프로그래밍 할 때는 그냥 unsigned int라고 생각하면 된다.

    - /usr/include/asm-generic/errno-base.h (기본적인 error들)  
        ⇒ 시스템 상에서 커널이 발생시킬 수 있는 에러들
    - /usr/include/asm-generic/errno.h

    ⇒ 여기 두 개의 경로에 들어가보면 어떤 error가 있는지 확인할 수 있다.

    > getpid()를 통해 자신의 프로세스 번호를 출력하는 프로그램 작성  
    > getppid()를 통해 자신을 생성한 부모 프로세스의 번호를 출력하도록 수정
    >
    > ⇒ 리눅스에서 모든 프로세스는 항상 다른 프로세스에 의해 실행된다. 최초의 프로세스인 init process (or systemd)가 천천히 필요한 process들을 생성한다.

    ```c
    #include <stdio.h>
    #include <sys/types.h>
    #include <unistd.h>

    int main(void) {

        pid_t pid_num = getpid();
        pid_t ppid_num = getppid();

        printf("pid_num : %d\nppid_num : %d\n", pid_num, ppid_num);

        return 0;
    }
    ```

    ./pid를 해보면, ppid는 9가 나온다. ps를 입력하면, 9번은 bash인데, 이를 보면 shell에서 프로그램을 실행하면 bash가 실행한다.

    - strace

    man strace - trace system calls and signals. 설명에 괄호가 있는 것은 생략이 가능하다는 뜻이다.

    strace ./pid (방금 만든 프로그램) 을 입력하면, 이 프로그램을 실행할 때 필요하나 모든 시스템 콜이 나오게 된다. 가다가 보면 getpid() getppid()가 나오게 된다. printf는 write라는 system call을 사용하는데, 사실 printf는 write라는 system call을 활용한 껍데기 라이브러리이다.

    ---

    여기까지는 굉장히 간단하나 시스템 콜들이었고 이제는 파일에 관련된 시스템 콜들에 대해서 살펴본다. ⇒ man open

    일단 파일을 생성하게 되면, 접근 권한을 설정해야 한다. creation으로 flag를 설정할 때만 mode라는 parameter가 의미를 갖게 되는데, 이는 접근 권한을 의미한다.

    RETURN VALUE를 보면, 실패할 경우 -1을 return하는데, 왜 error가 발생했는지 알려면 perror&lt;errno.h&gt;를 활용해서 error handling을 하면 된다. perror()를 사용하면, 현재 에러가 어떤 에러인지 그 내용을 string으로 보내주고, perror에 넣는 parameter가 main이라면,

    main: error string… ⇒ 이와 같이 출력된다.

    > command line에서 filename을 입력받아서 처리하는 프로그램
    >
    > 명령어 뒤에 붙는 (2)는 system call이라는 표시이다.

    ```c
    #include <stdio.h>
    #include <sys/types.h>
    #include <sys/stat.h>
    #include <fcntl.h>
    #include <stdlib.h>
    #include <unistd.h>
    #include <errno.h>

    // argc는 parameter의 개수. 보통은 한 개를 넣으면 2인데,
    // 2가 아니면 check를 해준다.

    // argv[]는 전달된 parameter
    // char* argv[] => pointer에 대한 pointer. c언어에서 string은 포인터이다.
    // 즉, 문자열들을 가리키는 포인터를 가리키는 포인터라는 뜻!
    // char** argv 라고 쓰기도 한다.
    int main(int argc, char* argv[]) {

        if (argc != 2) {
            printf("opentest filename\n");
            exit(0); // 시스템 콜은 아니고 라이브러리인데, 프로그램을 종료하는 것이다.
        }

        printf("first parameter: %s\n", argv[0]);
        printf("second parameter: %s\n", argv[1]);

        // your code comes here...
        // ...

        int fd = open(argv[1], O_RDONLY); // file descriptor. argv[0]는 실행한 command

        if (fd == -1) {
            perror("first open "); // comment를 덧붙여줌.
            exit(0);
        }
        else {
            printf("file %s successfully opened!\n", argv[1]);
        }

        close(fd);

        return 0;
    }
    ```

---

- read

ssize_t read(int fd, void \*buf, size_t count);  
⇒ fd는 file descriptor, buf는 data를 받아올 buffer, count는 읽을 byte 수

- write

ssize_t write(int fd, const void \*buf, size_t count);  
⇒ fd는 file descriptor, buf는 file에 wirte할 내용이 담긴 주소, count는 쓸 byte 수

read와 write 모두 error가 발생하면 -1을 체크해준다.

---

> Unix의 cp 명령어와 유사한 mycp 프로그램을 작성하자
>
> mycp는 첫번째 인자로 지정된 파일을 두번째 인자로 지정한 파일로 복사한다.
>
> \$ mycp source-file dest-file
>
> dest-file의 모드는 rw-rw-rw-로 세팅하라.  
> (S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH) 또는 0644

```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>

#define BUF_SIZE 64

// argc는 parameter의 개수. 보통은 한 개를 넣으면 2인데,
// 2가 아니면 check를 해준다.

// argv[]는 전달된 parameter
// char* argv[] => pointer에 대한 pointer. c언어에서 string은 포인터이다.
// 즉, 문자열들을 가리키는 포인터를 가리키는 포인터라는 뜻!
// char** argv 라고 쓰기도 한다.
int main(int argc, char* argv[]) {

    if (argc != 3) {
        printf("opentest filename\n");
        exit(0); // 시스템 콜은 아니고 라이브러리인데, 프로그램을 종료하는 것이다.
    }

    printf("first parameter: %s\n", argv[0]);
    printf("second parameter: %s\n", argv[1]);

    // C에서도 마찬가지고, Shell script에서도 마찬가지로
    // 숫자를 바로 쓰는 것은 좋은 습관이 아니다.
    char* read_buf = malloc(sizeof(char)*BUF_SIZE);

    int source_fd = open(argv[1], O_RDONLY); // file descriptor. argv[0]는 실행한 command
    int dest_fd = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, 0644); // O_TRUNC : 파일이 있으면 내용을 밀어라

    if (source_fd == -1 || dest_fd == -1) {
        perror("An error occured on opening.\n"); // comment를 덧붙여줌.
        exit(0);
    }
    
    for (;;) {
        int read_size = read(source_fd, read_buf, BUF_SIZE);

        // file의 끝, EOF에 도달한 경우.
        if (read_size == 0) {
            free(read_buf);
            break;
        }

        write(dest_fd, read_buf, read_size);
    }

    close(source_fd);
    close(dest_fd);

    return 0;
}
```

→ ./myCP ex1.c copy2.c … ls -l을 하면, file size가 모두 똑같다. 실행파일도 복사가 가능하지만, 복사를 하면 실행 권한이 없어지기 때문에 실행이 불가능하다. chmod를 통해 실행 권한을 얻으면 실행할 수 있다.

> !xxx 를 하면, 가장 최근에 xxx로 실행된 command 재수행
