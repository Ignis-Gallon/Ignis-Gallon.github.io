---
title: "오픈소스SW설계"
---

# 오픈소스SW설계

2학년 2학기에 수강한 과목의 노트입니다. Linux 명령과 Shell Script, gcc와 Makefile, Git을 거쳐 시스템 콜, 프로세스, 시그널, 쓰레드까지 Linux 위에서의 개발 도구와 시스템 프로그래밍을 다룹니다. 주차마다 온라인 강의 노트와 실습 노트가 짝을 이룹니다.

**주차별 노트**

- [1주차 온라인 강의: 오픈소스SW, Linux 기본 명령, I/O redirection](01-week01-online-oss-linux-basics.md): 오픈소스SW의 역사와 라이선스, 부팅 과정, 기본 명령, I/O redirection과 Pipe, 파일 검색
- [2주차: 디렉토리 구조, Editor, Pipe](02-week02-directory-editor-pipe.md): tree 명령, 주요 디렉토리의 역할, 에디터, I/O redirection, Pipe
- [2주차 온라인 강의: Processes, File Operations](03-week02-online-processes-file-operations.md): 프로세스 종류와 상태, ps와 top, Background/Foreground, 파일 시스템과 Mount, 압축
- [3주차: ps, Job, 압축](04-week03-ps-job-compression.md): ps 옵션과 PID, 프로세스 개수 세기, Job과 fg/bg, tar 압축, diff
- [3주차 온라인 강의: 사용자 환경, 파일 소유권, 텍스트 처리](05-week03-online-user-env-ownership-text.md): 사용자와 그룹, sudo, 환경 변수, chown·chgrp·chmod, sed·awk·sort·grep
- [4주차: 유저 추가, chmod, 환경 변수, awk, 정규표현식](06-week04-user-chmod-env-awk-regex.md): /etc/passwd와 sudo 그룹, ssh와 su, 접근 권한, PATH, awk, 정규표현식
- [4주차 온라인 강의: Shell Script](07-week04-online-shell-script.md): 함수, 변수와 export, 스크립트 인자, 조건문, 산술식, 반복문
- [5주차: 실습 문제(Exit Code, Shell Script)](08-week05-practice-exit-code-shell-script.md): echo \$?와 종료 코드, &&·||·;의 차이, 함수와 반복문 실습 문제 Q1 ~ Q4
- [5주차 온라인 강의: gcc, Static Library, Shared Library](09-week05-online-gcc-static-shared-library.md): 실행 파일 생성 과정, gcc 구조와 옵션, 정적·공유 라이브러리 만들기
- [6주차: 컴파일 과정, 라이브러리 실습](10-week06-compile-library-practice.md): 전처리·컴파일·어셈블·링크 단계별 실습, 여러 파일로 나눠 컴파일하기, 정적 라이브러리
- [6주차 온라인 강의: Makefile, Recursive make](11-week06-online-makefile-recursive-make.md): Makefile이 필요한 이유, 구조와 매크로, phony target, Recursive make
- [7주차: Make 실습](12-week07-make-practice.md): 리눅스 커널 소스로 본 Make의 필요성, 규칙 작성, suffix 규칙, 매크로, 헤더 의존성
- [9주차 온라인 강의: 소스 버전 관리, Git 소개](13-week09-online-version-control-git.md): 버전 관리 시스템의 역할, Git의 저장 방식과 작업 흐름, branching과 merging
- [10주차: Git 실습](14-week10-git-practice.md): git log 옵션, init·add·commit, .gitignore, checkout, branch와 merge, 원격 저장소
- [10주차 온라인 강의: 시스템 프로그래밍, 시스템 콜](15-week10-online-system-programming-system-call.md): 커널과 User/Kernel Space, 시스템 콜과 함수 호출의 차이, POSIX, 에러 처리, 파일 입출력
- [11주차: 시스템 콜과 파일 입출력](16-week11-system-call-file-io.md): man 페이지와 errno, getpid·getppid, strace, open·read·write로 만드는 mycp
- [11주차 온라인 강의: 프로세스 소개, 프로세스의 관리](17-week11-online-process-management.md): 프로세스의 두 가지 추상화, Context Switching, 상태와 구성, fork·exec·exit·wait
- [12주차: 프로세스 시스템 콜](18-week12-process-system-call.md): /proc, fork와 wait, orphan·zombie process, exit code, execve, system
- [12주차 온라인 강의: 시그널, 알람 타이머](19-week12-online-signal-alarm-timer.md): 시그널의 개념과 기본 동작, kill과 raise, signal과 sigaction, sigprocmask, alarm과 sleep
- [13주차: 시그널의 종류와 실습](20-week13-signal-practice.md): 주요 시그널, kill 명령, gdb, sigaction으로 핸들러 등록하기, alarm
- [13주차 온라인 강의: 쓰레드, Multi-Threading](21-week13-online-thread-multi-threading.md): 쓰레드와 프로세스의 메모리 구조, Pthreads API, 쓰레드 생성과 종료, join과 detach
- [14주차: pthread 실습](22-week14-pthread-practice.md): pthread_create와 pthread_join, 쓰레드 100개 만들기, volatile, 쓰레드에 인자 넘기기
- [14주차 온라인 강의: 쓰레드 동기화 문제](23-week14-online-thread-synchronization.md): 공유 변수 cnt가 어긋나는 예, Critical Section, mutex로 동기화하기

**기말 총정리**

학기 말에 주제별로 다시 정리한 노트입니다.

- [Git](24-final-git.md): 버전 관리 시스템, Git의 저장 방식과 작업 흐름, 명령어, branching
- [System Call & File Descriptor](25-final-system-call-file-descriptor.md): 커널과 시스템 콜, POSIX, errno, File Descriptor, open·read·write·lseek
- [Process](26-final-process.md): Context Switching, 프로세스 상태와 구성, fork·execve·exit·wait, orphan·zombie process
- [Signal](27-final-signal.md): 시그널의 개념과 대표 시그널, kill과 raise, sigaction, Signal Set과 Mask, 타이머와 sleep
- [Thread](28-final-thread.md): 스레드의 개념과 장점, Pthreads, 스레드 생성·종료·join·detach, volatile
- [Thread Synchronization](29-final-thread-synchronization.md): 공유 변수에서 생기는 문제, Critical Section, mutex
