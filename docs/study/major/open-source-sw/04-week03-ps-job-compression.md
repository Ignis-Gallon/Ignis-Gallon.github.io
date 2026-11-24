---
title: "3주차: ps, Job, 압축"
date: 2022-10-02
last_modified_at: 2022-10-18
category: major
tags: [오픈소스SW설계, Linux, 프로세스, tar]
---

# 3주차: ps, Job, 압축

- 컴퓨터에서 실행되고 있는 프로그램(Process)들은 대부분 컴퓨터가  
    부팅될 때 자동으로 실행되는 프로세스들이다.

`pipe를 이용하면 프로세스가 몇 개인지 쉽게 셀 수 있다.`

프로그램들이 돌아갈 때 필요한 것?  
CPU, 메모리, 디스크 ... 디스크는 무슨 역할을 하는가?  
=&gt; 디스크는 파일 입출력, MB/s 초당 몇 MB를 쓰고 받느냐.  
네트워크도 마찬가지, Mbps. Mega bit per seconds.

프로그램을 실행시킨다는 것은 프로그램이 메모리에 load되는 것.

## `현재 실행되고 있는 프로세스를 확인하는 명령어 ps`

man ps -&gt; ps : ps에 대한 설명이 출력된다.

- `ps`  
    bash, ps 라는 process를 실행시킴.  
    현재 terminal에서 실행되는 process만 보여준다.

    - 시스템에서 동작하는 모든 프로세스를 보려면  
        `ps -e`
    - 정보를 더 많이 얻을려면 -f 추가.  
        `ps -ef`

`Q. ps -ef를 쓰면, 현재 실행되고 있는 모든 프로세스가 나온다. 프로세스의 개수를 세려면?`

```shell
ps -ef | wc -l
```

맨 위에 나오는 UID 라인은 제외해야 하며, 실제로는 `ps -ef | wc -l`을 실행하면, `ps -ef`가 잠깐 실행됐다가 멈추게 되므로 `ps -ef`에 대한 라인도 카운트 돼서 1개가 늘어난다.  
→ 당연한 결과, 이것은 pipe를 이용한 command 이므로, 결과는 다음과 같다고 볼 수 있다.

```shell
~$ ps -ef
#
# contents...
#

# 이 위까지 모두 출력하고, 출력된 모든 line에 대하여 wc -l을 수행한다.
```

> 윈도우에서는 PID를 몰라도 되지만, 리눅스에서는 PID가 굉장히 중요하다.

`Q. PPID` : 부모 프로세스의 ID. 프로세스는 반드시 다른 프로세스로부터 생성된다. 리눅스에서는 새로운 프로세스(Child Process)는 반드시 이미 실행되고 있는 부모 프로세스로 부터 실행된다.

`ps -ef`는 `-bash`로부터 생성된다. (bash가 Parents, PPID가 -bash의 PID이다.)  
bash는 프로그램을 실행시켜준다. (깜빡거리고 있는 것도 bash 프로그램)  
Linux에서 입출력을 제어해주는 것을 `bash`라고 한다. `bourne again shell -> bash`

> bash는 /init으로부터 만들어진다. init은 리눅스가 실행될 때 최초에 실행되는 프로세스이다.

ls /sbin/init (sbin 디렉토리에 init 프로그램이 존재)

최근에는 systemd라는 프로그램으로 link를 걸어놨다. (소프트 링크)

wsl에서는 ps -ef를 하면 /init이 굉장히 많이 존재하는데, 이는 wsl의 특징이다.

- 시스템이 실행되면 `init(systemd)`이 실행되고, 하나씩 가지를 치면서 프로세스 트리를 만들어간다. (pstree -&gt; ps 트리 구조를 보여준다.)
- `ps -ef`를 했을때 `CMD`에 `[ ... ]` 라고 돼있는 것들이 있는데, 이런 것들은  
    윈도우에서 `Service`를 의미하며, `Linux`에서는 `Daemon`이라고 한다.
- `top` : 프로세스가 변하는 상황을 볼 수 있는 명령어

`Q. CPU가 20개인데 어떻게 617개의 프로세스가 돌 수 있는가?`

- `Process`에는 `State`가 있어서 필요한 프로세스는 `running` 하고,  
    그게 아니라면 `sleep`, `stop` 등 다른 `state`에서 대기 시키면 된다.  
    보통 `sleep`은 10ms 정도를 왔다갔다 하면서 `process`를 처리한다.  
    `-> 이를 Scheduling이라고 하며, OS에서 관장한다. 더 정확하게는 Kernel!`
- `Process`에는 `priority`가 존재하며, 작을수록(음수도 포함) 우선순위가 높다.  
    `Priority`가 `음수`인 것들은 대부분 우선순위가 엄청 높다. 이것들은 `Kernel`이 실행시킨  
    `Kernel Threads`.

결국은 갖고 있는 H/W 자원을 나눠서 사용해야 하기 때문에 `스케쥴링(Scheduling)`을 한다.  
=&gt; CPU 사용량이 100%라는 것은 CPU 1개를 다 쓰고 있다는 뜻.  
CPU는 코어가 여러 개이기 때문에, 한 개의 코어를 넘어서 사용하고 있다는 뜻.

---

## `About Job`

`Job`에 대해서 알기 위해서 프로그램을 작성해본다.

visual studio : code . =&gt; 현재 디렉토리를 연다.

// 원격 컴퓨터에 접속해서 사용하려면 Extensions에서  
// Remote - SSH 사용. 외부에 있는 컴퓨터에 네트워크로 접속해서  
// 개발이 가능하다.

vsCode에서 New Terminal 하면, wsl에서 쓰던 작업을 그대로 할 수 있다.

- 프로그램 만든 후, 컴파일 하는 법

```c
// mysleep.c
#include <stdio.h>
#include <unistd.h>

int main() {

    printf("I am sleeping...\n");
    fflush(NULL);
    sleep(30);
    // 메모리 버퍼라는 것이 있는데, 메모리 버퍼에 저장을 해놨다가
    // 어느정도 차야만 출력이 된다.
    // 세팅이 버퍼에 넣어놨다가 출력하는 상태라면, 저장되는 즉시 출력되도록 바꿔주어야 한다.

    return 0;
}
```

```c
// mysleep2.c
#include <stdio.h>
#include <unistd.h>

int main() {

    printf("I am sleeping...\n");
    fflush(NULL);
    sleep(30);
    printf("I am not sleeping...\n");

    return 0;
}
```

ex) gcc mysleep.c -o mysleep  
./mysleep.c  
-&gt; 프로그램 실행

`man sleep`

- manual에는 몇 가지 section이 있다. sleep 이라는 프로그램의  
    library를 보려면 man 3 sleep 을 쳐야 한다.

\$ man 3 sleep

vsCode는 Compiler와 Debugger가 들어있지 않다. Extensions에서 설치 필요.

`./mysleep` 을 입력하면, 프롬프트를 가진 채로 잠들어 버린다. `(foreground)`

`terminal(foreground)` 을 쓸 수 있는 프로그램은 한 번에 한 개만...  
`Ctrl + C`를 누르면 종료가 되고, `Ctrl + Z`를 누르면 `suspend(stopped)`가 된다.  
=&gt; 실행할 때, 뒤에다가 `&` 기호를 붙여주면, background에서 돌게 된다.

`./mysleep &`

- jobs를 입력하면 현재 터미널을 사용하려고 하는 process들을 확인할 수 있다.

background에서 돌고있는 process를 foreground로 오게하는 방법  
`fg (job number)`

> sleep이 먼저 된다? -&gt; man fflush... 메모리 버퍼에 꽉차야 출력이 된다.  
> fflush는 버퍼에 있는 녀석을 메모리에 쓰지말고 바로 출력해준다.  
> fflush(NULL); -&gt; 메모리 버퍼에 들어오는 녀석을 바로 출력하도록 해준다.

## `압축`

```shell
tar -zcvf opensw.tar.gz opensw2022/
tar -jcvf opensw.tar.bz2 opensw2022/
tar -Jcvf opensw.tar.xz opensw2022/

# unzip
tar xvf opensw.tar.gz
tar xvf opensw.tar.bz2
tar xvf opensw.tar.xz
```

size : 2829 -&gt; 2.8KB 정도 된다.  
실행파일은 실제로 Size가 되게 큰데, 압축하면 용량이 많이 줄어든다.

```shell
tar -jcvf opensw.tar.bz2 opensw2022/
tar -Jcvf opensw.tar.xz opensw2022/
```

-&gt; -Jcvf 성능이 가장 좋아서 최근에는 많이 사용하는 것 같음.  
확장자는 굳이 쓰지는 않아도 되는데, 사람이 이해하기 쉽게 하도록 확장자를 붙여서 저장해준다.

`rm -rf opensw2022/`  
-&gt; -f는 무조건 지우는 옵션, -r은 recursive하게 다 지우는 옵션.

`tar xvf opensw.tar.xz` -&gt; zip파일 open하는 방법은 똑같다.

cp mysleep.c mysleep2.c  
-&gt; 파일 복사 후, 파일 내용을 약간! 수정.

- `diff` : mysleep.c와 복사하고 약간 수정한 mysleep2.c를 만들었다.  
    바뀐 파일에서 어떤 내용이 바뀌었는지 체크하려면 diff를 사용한다.

`diff -u mysleep.c mysleep2.c` // diff 옵션 -u 예쁘게, -r recursive -w 공백 비교 제외 -N 없는것도  
차이점이 나온다.

vsCode에서 비교할 파일을 선택하고, right click을 해서 선택 항목 비교를  
해주면, 차이를 보여준다.  
-&gt; 그런데, GUI에서 사용되는 대부분의 기능은 사실 프로그램을 불러와서 사용하는 것. diff 이용.

- 숙제 중에서, 리눅스에서 간단한 텍스트 파일을 작성하고, 이 파일을 윈도우에서 열어서 확인

    ex) nano /mnt/c/Temp/Helloworld.c → 이와 같이 file을 /mnt directory에 mount 되어있는  Windows Drive에 생성 가능.
