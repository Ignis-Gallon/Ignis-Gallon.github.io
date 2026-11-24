---
title: "과제1"
date: 2023-04-11
last_modified_at: 2023-04-20
category: major
tags: [운영체제, CPU 스케줄링, Python, Matplotlib]
---

# 과제1

- Round Robin의 Time Quantum은 전체 수행시간의 평균을 / 2 한 것으로 설정한다.
- 만들어야 할 리스트

FCFS

(Preemptive/Non-Preemptive) SJF  
(Preemptive/Non-Preemptive) SRTF

(Preemptive/Non-Preemptive) Priority  
RR  
Priority RR(Priority Level Queues)

Feedback

Real-time 2개

---

[Python | Basic Gantt chart using Matplotlib - GeeksforGeeks](https://www.geeksforgeeks.org/python-basic-gantt-chart-using-matplotlib/)

matplotlib.pyplot을 사용하는데, broken_barh를 사용해서 중간 중간 띄어진 것을 표현할 수 있다.

Cell 실행을 하면 출력할 수 있는 Text의 양에 한계가 있기 때문에 따로 Text Editor를 켜야만 한다는 한계점이 있다. 따라서 Interactive Cell을 사용하지 않고 Matplotlib.pyplot을 사용하면 따로 figure 창이 켜지게 되는데, 이렇게 되면 figure 창을 닫아야만 다음 동작이 수행된다.

이를 이용해서 각각의 Scheduling에 대한 결과를 하나 출력하고, 창을 닫으면 그 이후의 결과를 출력하는 방식을 사용해보아도 될 것 같다.

> Preemptive와 Non-Preemptive의 동작이 매우 유사해서, 달라지는 function만 인자로 넘겨줘서 수행하도록 코드를 수정할 수 있다.  
>   
> 2023.04.15  
> 지금   
> SJF ↔ SRTF,  
> PSJF ↔ PSRTF ↔ Priority  
> 에서 사용되는 startScheduling이 매우 유사한 형태로 짜여져 있고, 코드의 중복이 매우 심한 상태이다. 이를 줄이려면 switch나 if문을 통해 실행되는 코드만 바뀌게 하는 방법을 도입할 수도 있을 것이다.  
>   
> 그럴려면 superclass에서 level을 정해서 코드를 나눠 구분되도록 만들고, sorting 함수는 모두 이름을 갖게 한 다음 overriding으로 구분하도록 만들어도 될 것 같다.  
>   
> Round Robin은 다소 차이가 있을 수도 있다. 그리고, startScheduling을 non-preemptive와 preemptive로 구분해서 만들 수도 있을 것. 두 개를 다 합쳐버리려면 다소 코드가 많이 복잡해질 수도 있을듯.

---

**PSJF**

코드를 짜던 도중에 프로그램에 오류가 발생하지는 않지만, 프로그램 실행이 안되는 현상이 발견되었다. 디버깅을 해보니 프로그램이 무한 루프에 빠졌었는데, 새롭게 추가된 프로세스에 대하여 startRunning()을 실행하지 않아서 이런 현상이 발견한 것을 확인했다.

이를 추가해주니 결과가 맞게 나오는 것을 확인 했는데, 문제는 끝나는 순서대로 Process의 순서를 정하다보니, arrivalTime이 0인 프로세스가 Process 2로 나오는 문제가 발생했다. 이는 도착한 순서대로 Process의 이름을 지정해주도록 코드를 추가하면 해결할 수 있다.

⇒ 다만 끝나는 순서대로 1번으로 지정해도 되는 것으로 보인다.

테스트를 하던 도중에 문제가 발견됐다. 파이썬에서는 함수의 인자로 리스트를 넘겨주면 deep copy가 돼서 문제가 발생할 수 있다. (그 안에 Process 클래스가 들어있음.)

**SRTF**

이전에 만들어두었던 PSJF와 비슷한 맥락이었음.

다른 것은 오직 sortByBurstTimeAsc → sortByRemainingTimeAsc 뿐이다.

**Priority**

(priority의 수가 낮을 수록 우선순위가 높다.)

현재는 매 waitingTick마다 priority가 1씩 작아지도록 만들었다.

⇒ 현재 코드의 구조가, 매 class마다 유사한 구조가 매우 많다. 과도하게 중복이 많이 되는 것으로 보여서 class 설계가 잘못된 거라고 생각할 수밖에 없음.
