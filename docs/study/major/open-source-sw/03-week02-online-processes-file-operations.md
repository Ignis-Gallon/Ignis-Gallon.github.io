---
title: "2주차 온라인 강의: Processes, File Operations"
date: 2022-10-02
last_modified_at: 2022-10-20
category: major
tags: [오픈소스SW설계, Linux, 프로세스, 파일 시스템]
---

# 2주차 온라인 강의: Processes, File Operations

## `Processes`

- `Process`

    - 프로세스란, 현재 실행되고 있는 프로그램을 말한다.  
        Shell에서 command를 입력하거나 GUI에서 프로그램을 실행하면 프로그램이 메모리에 load되어 실행되는데, 이를 Process라고 한다.
    - `Process`는 `시스템 자원`(H/W, CPU, I/O Devices, Memory)을 사용한다.
    - OS(특히, kernel)은 각각의 프로세스에 필요한 H/W 자원을 할당하는 역할을 담당한다.

- `Process Type`

    1. `Interactive Process` : 상호작용하는 프로세스로, Command or GUI Click으로 실행한다.  
         ex) ls, pwd, top, find, bash 등…
    2. `Batch Process` : 상호작용이 없는 프로세스 (특정 시점이 되면 자동 수행), 사용자는 `Batch Process`의 실행을 알지 못한다. → 자동적으로 실행되고, 알아서 수행 종료된다.  
        ex) ldconfig, updatedb(System Update를 정기적으로 뒤에서 돌면서 checking) 등…
    3. `Daemon` : 사건이 일어나길 기다리는 프로세스로, 시스템이 시작될 때 자동으로 함께 시작된다. 특정 사건이 일어나면 이를 처리하는 서비스이다.  
        ex) httpd, sshd, libvirtd 등… → httpd : 사용자가 웹에서 주소를 입력하면 http daemon process가 요청한 page를 가져와서 보여주는 것이다.
    4. `Threads` : 하나의 process는 보통 한 가지 일을 하며, 여러 가지 일을 동시에 처리하고 싶을 때 thread를 사용한다. 하나의 프로세스는 메모리 공간을 공유하면서 동시에 여러가지 thread를 가질 수 있다.  
        ex) firefox 등…
    5. `Kernel threads` : kernel도 하나의 program(process)이다. kernel 안에서 여러 개의 job을 동시에 수행하고자 할 때, kernel thread를 사용한다. (ps command를 수행하면 확인 가능)  
        ex) Kthreadd, ksoftirqd 등…

- `Process Scheduling and States`

    - Process share a (or a few) CPU(s) → 최근에는 multi-core CPU로 인해 CPU가 여러 개 존재
    - Kernel은 process의 priority(우선순위)에 따라 해당 process가 어떤 CPU에서 실행될 지를 결정하고, 실행한다.
    - Process는 상태가 존재해서, 어떤 process가 수행되고 있으면, 다른 process는 대기한다.  
        → running state, sleep state …

- `Process IDs`

    - OS는 각각의 process에 고유한 Process ID를 부여한다.
    - OS는 process의 state를 관리하기 위해 PID를 사용한다.

> Linux에서는 모든 Process가 다른 Process에 의해 만들어진다. 시스템 부팅 시에 kernel에 의해서 가장 먼저 실행되는 init process(혹은 systemd)가 있으며, 이 process가 다른 process를 생성하고, 이 process가 또 다른 process를 생성하며 시스템을 구성하게 된다.

1. Process ID (PID) : Unique ID of a process.
2. Parent Process ID (PPID) : ID of a parent that started this process.
3. Thread ID (TID) : 하나의 process는 여러 개의 thread를 가질 수 있으며,  
    각각 고유한Thread ID를 가진다.

- `ps` : 현재 동작하는 process들에 대한 정보를 볼 수 있다.  
    → process는 그 종류가 굉장히 다양하며, 유저가 실행하지 않아도 Batch Process, Daemon, Threads, Kernel Threads가 돌고있다.

    - `pstree` : process 구조를 tree diagram으로 보여준다.

```shell
#displays all processes in the system
ps -ef

#more info about Thread
ps -eLf
```

- `top` : top을 실행하면, 현재 실행되고 있는 process의 status가 주기적으로 update된다.  
    top은 현재 process들이 잡아먹는 CPU 사용량과 메모리 공간을 보여준다.
- `Terminating a Process`

```shell
kill -SIGKILL <pid>

#or

kill -9 <pid>
```

- `Background and Foreground Processes`

    Linux Shell에서 command를 실행시키면 생성되는 process를 `job`이라고 한다.

    - `foreground job`

        foreground job은 실행되고나면 마칠 때까지 대기하게되며, 쉘에서 입력을 받을 수 없게 된다.  
        (ls 등과 같은 프로그램들 또한 foreground job이지만, 용량이 작아서 거의 바로 끝남)  
        길게 도는 job들은 foreground에서 실행되면 shell을 사용할 수 없게 되므로, background를 이용해서 job을 수행해야 한다.

    - `background에서 job을 수행하는 방법`

        ex) updatedb `&`

    - `CTRL-Z` : foreground job 을 잠시 중단한다.
    - `CTRL-C` : foreground job을 종료한다.
    - `bg`, `fg` : job을 foreground와  background에서 수행하기 위해 bg, fg command 이용
    - `jobs` : 현재 수행되고 있는 모든 job을 보여주며, background에서 수행되는 것은 &표시

    ```shell
    sleep 100 &
    # &를 이용해 job이 background에서 수행되도록 만든다.

    sleep 200 &
    # 위와 동일한 방식

    jobs
    # 현재 수행되고 있는 job list를 출력한다. background에서 수행되는 job은 & 표시

    fg 2
    # background job 2를 foreground에서 수행하도록 바꾼다.

    Ctrl-Z
    # foreground job을 잠시 중단한다.

    bg 2
    # job number 2를 background에서 수행한다.
    ```

## `File Operations`

- Linux & Unix 에서는 모든 것이 File이다. 즉, 모든 것을 File로 취급한다. System에 붙은 Device도 File로 관리할 수 있다. (/device의 sub directory에 mount되어 관리된다. USB는 /media)
- Filesystem은 tree구조로 되어있으며, tree의 가장 상위에 있는 directory는 `root directory(/)`
- A disk is divided into a few partitions. Use each partition for different purposes.  
    Each filesystem occupies a disk partition.  
    All files are stored in a single partition.
- `Mount` : 특정 Partition에 있는 File System을 root directory에 있는 tree의 특정 지점에 갖다 붙이는 것을 Mount 라고 한다.

```shell
sudo mount /dev/sda5 /home
# sda (disk_name) 상에서 다섯 번째 partition을 /home directory에 mount한다.
# home directory에 data를 생성하면, sda5 partition에 저장된다.

sudo umount /home
# unmount sda5 from /home
```

> wsl에서 umount한 C, F 드라이브는 다음 방법을 이용하면 다시 mount할 수 있다.

```shell
sudo mount -t drvfs C: /mnt/c
sudo mount -t drvfs F: /mnt/f
```

- `Overview of Directories`

    - `/proc` : Virtual filesystem; contains system information files
    - `/sys` : Similar to /proc, but can be used to alter system parameters
    - `/dev` : devices nodes; used by most H/W, S/W devices

    → 이 directory들은 진짜 파일 시스템이 아니다. 시스템이 작업을 수행하면서 정보를 표시하거나 kernel의 특정 기능에 접근하고 싶을 때, 위 세 개의 directory에 있는 파일들을 읽거나 씀으로써 시스템의 상태를 바꾸거나 읽어오는 등, kernel을 통해 제어할 수 있게 된다.

- `diff` : 한 파일과 다른 파일이 무엇이 다른지 비교해준다. (different) or 한 디렉토리와 다른 디렉토리 파일이 무엇이 다른지 비교하는 command.  
    → 만약 어떤 파일을 수정했는데, 수정본과 원본 파일의 차이를 보고 싶을 때 사용 가능.

- diff options

| Option | Usage |
|---|---|
| -u | Output unified context (standard diff format) |
| -r | Recursively compare any subdirectories found |
| -w | Ignore differences in spaces and tabs |
| -N | 없는 파일도 비교하는 옵션 |

- `rsync` : Backup할 때 주로 사용되며, 한 파일이나 디렉토리의 내용을 백업할 때 사용한다.  
    cp command를 이용해도 되지만, cp는 같은 File system 내에서만 백업이 가능한 반면,  
    rsync는 원격 컴퓨터에도 백업이 가능하다.

Use rsync carefully with —dry-run (or, -n) options

```shell
rsync -arv testdir/ user@<IP 주소>:testdir
# 같은 컴퓨터가 아니라, 해당 IP의 컴퓨터로 복사한다.
```

a : archive → 파일의 속성 변경 금지  
r : recursive → recursive하게 모든 sub directory 탐색  
v : verbose → 설명을 상세하게 요청 (verbose : 말이 많은)

- `Compressing Data`

    - File의 Size를 줄인다.
    - 여러 디렉토리에 있는 File들을 하나로 뭉친다. (그룹을 모아서 하나의 File화)

- `gzip` : Linux 기본 압축.  
    → Uncompression : `gzip -d` or `gunzip`
- `bzip2` : gzip보다 size를 더 작게 압축하는 방법.  
    → Uncompression : `bzip -d` or `bunzip2`
- `zip` : Windows 파일을 압축할 때 사용되는 압축 방법.  
    → Uncompression : `unzip`

- `Archiving and Compressing Data Using tar`

    `tar` : 파일을 하나의 저장소에 모으고 압축하는데 사용되는 유틸리티이다.

    | Command | Usage |
    |---|---|
    | tar cvf mydir.tar mydir | Group files in mydir into mydir.tar file |
    | tar xvf mydir.tar | Extract all files in mydir.tar into mydir directory |
    | tar zcvf mydir.tar.gz mydir | Create the archive for mydir and compress with gzip |
    | tar jcvf mydir.tar.bz2 mydir | Create the archive for mydir and compress with bzip2 |
    | tar xvf mydir.tar.gz | Extract all files in mydir.tar.gz into mydir directory<br>Note : You do not have to tell tar it is in gzip format |

```shell
tar cvf mydir.tar mydir; gzip mydir.tar
gunzip mydir.tar.gz; tar xvf mydir.tar

or

tar zcvf mydir.tar.gz mydir; tar jcvf mydir.tar.bz2
tar xvf mydir.tar.gz; tar xvf mydir.tar.bz2
```
