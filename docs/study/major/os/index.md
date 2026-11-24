---
title: "운영체제"
---

# 운영체제

3학년 1학기에 수강한 운영체제 수업의 노트입니다. 컴퓨터 시스템 구조와 운영체제의 역할에서 시작해 프로세스와 스레드, CPU 스케줄링, 프로세스 동기화, Deadlock, 메모리 관리(Paging, Segmentation, Virtual Memory)까지 다룹니다. 학기 후반부에 함께 진행된 8086 어셈블리 수업의 노트는 따로 묶었습니다.

**주차별 노트와 과제**

- [과제1](01-assignment-1.md): CPU 스케줄링 알고리즘 구현 과제의 진행 메모, 구현 목록, Matplotlib으로 Gantt chart 그리기
- [과제2](02-assignment-2.md): 스레드 기반 콘솔 게임 과제, 예제 C 코드
- [conio.h: 콘솔 키 입력 함수](03-conio-h.md): kbhit, getch, getche, ungetch의 동작과 반환값
- [Plan: 스레드 기반 콘솔 게임 구현 계획](04-assignment-2-plan.md): 게임 동작 구현 뒤 함수를 Thread로 나누는 순서, 키 입력 스레드와 공유변수 lock
- [1주차: 컴퓨터 시스템 구조, 운영체제의 관점](05-week-01.md): 운영체제의 정의, 컴퓨터 시스템 구성, User View와 System View
- [2주차: 현대 컴퓨터 구조, Multiprocessor](06-week-02.md): 폰 노이만 구조, Multiprocessor의 장점, Symmetric·Asymmetric 방식, Multi-core
- [3주차: OS 서비스, 시스템 동작](07-week-03.md): UI·프로그램 실행·I/O·파일 시스템·통신·오류 검출 등 운영체제가 제공하는 서비스
- [4주차-1: Process, Context Switching, Thread, 스케줄러](08-week-04-1.md): 프로그램과 프로세스의 차이, PCB, Context Switching, Process Scheduling
- [4주차-2: 프로세스 연산, IPC, Thread 모델](09-week-04-2.md): 프로세스 생성과 종료, fork, Zombie Process, 두 가지 IPC 방식, 스레드 모델
- [5주차-1: CPU Scheduling 기본 개념과 평가 기준](10-week-05-1.md): 멀티프로그래밍·멀티태스킹·멀티프로세싱, CPU burst와 I/O burst, 단기·중기·장기 스케줄링
- [5주차-2: SRTF, Priority Scheduling](11-week-05-2.md): 선점형과 비선점형, 우선순위 부여 방식, Starvation과 Aging
- [Assignment: FCFS와 스케줄링 평가 기준](12-week-05-2-assignment.md): FCFS 성능 분석, CPU 이용률·처리율·반환시간·대기시간·응답시간
- [6주차-1: Multiple-Processor Scheduling, Real-Time CPU Scheduling](13-week-06-1.md): Multicore CPU, Multithreaded core, NUMA, SMP 구현 방식
- [6주차-2: Real-Time Scheduling, Rate Monotonic](14-week-06-2.md): 데드라인과 주기, rate, Rate Monotonic Scheduling
- [7주차-1: Process Synchronization](15-week-07-1.md): 공유 데이터와 race condition, 임계 구역 문제
- [7주차-2: 임계 구역 조건, Peterson's Solution](16-week-07-2.md): 임계 구역 문제의 해결 조건, flag만 쓰는 알고리즘의 문제점, Peterson's Solution의 한계
- [9주차: 동기화의 하드웨어 지원, TestAndSet](17-week-09.md): Bakery Algorithm의 한계, atomic 연산의 필요성, TestAndSet
- [10주차: Deadlock](18-week-10.md): Deadlock의 개념, 예방·회피·탐지·회복
- [11주차: Memory Management, 주소 바인딩](19-week-11.md): Binding과 주소 공간 사상, 기호 주소와 실제 주소, 컴파일 시간 바인딩
- [12주차: Contiguous Allocation](20-week-12.md): 연속 메모리 할당, Base·Limit Register로 접근 범위 검사하기
- [13주차: Paging](21-week-13.md): 단편화 문제, 논리 주소와 물리 주소, Page Table을 이용한 주소 변환
- [14주차: Page Table, Segmentation, Virtual Memory](22-week-14.md): PTBR·PTLR, TLB와 Effective Access Time, Hierarchical Paging, Hashed Page Table
- [Window Thread Synchronization](23-window-thread-synchronization.md): Windows 스레드 동기화 예제 C 코드

**Assembly**

- [9주차 (Assembly)](24-assembly-week-09.md): 어셈블리 수업의 학습 목표, 첫 예제 코드와 디버거로 한 줄씩 실행해 보기
- [12주차 (Assembly)](25-assembly-week-12.md): 무조건 분기 JMP와 조건 분기, 스택, 서브루틴, 문자열 출력 예제
- [Assembly-1: 시스템 콜, 명령어 사이클, 8086 레지스터](26-assembly-1.md): 명령어 인출·해독·데이터 인출·실행 사이클, 8086 Register의 종류, 주소 지정
- [Assembly-2: Segment와 Offset, 명령어 형식](27-assembly-2.md): 16비트 Segment와 Offset으로 1MB 주소 만들기, 명령의 구성, 명령어를 기계어로 바꾸기
- [Assembly-3: 간단한 프로그램 구조, 데이터 정의](28-assembly-3.md): DB·DW·DD, ASSUME으로 세그먼트 구성하기, 주소 지정 방식, ADD와 ADC
- [Assembly-4: 비교와 분기, 반복](29-assembly-4.md): INC·DEC, 키 입력 시스템 콜, CMP와 조건 분기, LOOP, 스택과 서브루틴
- [Assembly-5: 컴파일과 디버깅 과정](30-assembly-5.md): 디버거 화면에서 레지스터·IP·Flag 읽기, 명령어와 기계어의 대응
- [Assembly-6: 세그먼트 구성과 프로그램 시작점](31-assembly-6.md): 데이터가 코드로 해석되는 문제, Label로 시작점 지정하기, Data Segment 분리
- [Assembly-7: Offset 접근과 숫자 출력](32-assembly-7.md): 변수의 Offset으로 연속된 데이터에 접근하기, 두 자리 숫자 출력의 디버깅
- [Assembly-8: 두 자리 숫자 출력, 레지스터 보호](33-assembly-8.md): 자릿수별로 나눠 출력하기, stack으로 AX 값 보호하기
