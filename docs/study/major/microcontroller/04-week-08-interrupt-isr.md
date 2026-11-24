---
title: "8주차: 인터럽트, ISR"
date: 2022-10-24
last_modified_at: 2022-10-25
category: major
tags: [마이크로컨트롤러응용, AVR, 인터럽트, ISR, PCINT]
---

# 8주차: 인터럽트, ISR

- 인터럽트란?

프로그램 실행 중에 CPU의 현재 처리 순서를 중단시키고 다른 동작을 수행하도록 하는 것

외부로부터 인터럽트 요구가 들어오면,

1. CPU는 원래의 프로그램 수행을 중단하고,
2. 요구된 인터럽트를 위한 서비스 프로그램을 먼저 수행

<!-- -->

- 인터럽트 서비스 루틴(ISR, Interrupt Service Routine)

인터럽트를 처리하기 위하여 수행하는 프로그램 루틴.

---

폴링을 하려면, while을 돌면서 모든 조건을 체크해줘야 한다. 이를 개선하기 위해 필요한 것이 인터럽트이다. 인터럽트를 하기 위한 필수 요구 사항 몇 개가 있는데, 이를 살펴본다.

원래 프로그램은 순차적으로 실행이 되는데, i번 주소가 실행되고 있을 때 인터럽트가 발생한다면 i+1번지의 주소를 스택에 넣은 뒤, ISR로 이동한다. 그 뒤 ISR이 끝나면, 스택을 pop해서 다음 번지로 이동한다.

함수는 언제 불려오는가? 내가 원할 때 부를 수 있다. 하지만 인터럽트는 내가 원한다고 부를 수 없고, 반드시 해당하는 조건인 이벤트가 생겼을 때 CPU가 실행해준다.

---

- AVR에서의 인터럽트 처리

<!-- -->

1. 사용할 인터럽트 등록(세팅)
2. 인터럽트 방식 설정
3. 해당 인터럽트의 ISR 코딩
4. Global Interrupt Enable

인터럽트의 종류는 굉장히 많지만, 모든 인터럽트를 사용할 필요는 없다. 먼저 인터럽트를 세팅하고, 같은 인터럽트라도 이벤트가 발생하기 위한 세부조건도 optional하게 설정해줘야 한다. (있을 수도 있고 없을 수도 있음. 단, 외부 인터럽트는 이 조건을 설정해주어야 함.) 그리고 ISR을 작성한다. ISR 안에서는 어떤 인터럽트의 ISR인지를 반드시 명시해준다. ISR이 없다고해서 에러가 나지는 않지만, 그냥 무시하고 main을 실행하게 된다. 따라서 반드시 실행할 인터럽트에 대응되는 ISR을 작성해주어야 한다. 이렇게만 설정하고 실행하면 안되는 경우가 많은데, 이럴 때는 Global Interrupt를 Enable 해주어야 한다. 이 Flag를 Enable하냐 Disable하냐에 따라서 인터럽트를 실행하냐 안하냐를 결정할 수 있다. 초기값은 Disable이다. 인터럽트 세팅을 하는 동안에는 Disable된 상태로 세팅을 하고, 세팅이 끝나게 돼서 main loop에 들어가기 전에 Global Interrupt를 Enable해준다. 경우에 따라서는 Global Interrupt를 Disable 해야할 때도 있는데, 그럴 때는 적절히 조정해주면 된다.

---

Mega 2560은 PCINT23:0 이라는 24개의 외부 인터럽트와 INT7:0 외부 인터럽트가 사용 가능하므로,  총 32개의 외부 인터럽트가 사용 가능하다. → 이 정도 개수면 그냥 핀 하나마다 인터럽트를 설정해줄 수도 있다.

INT7:0 (기존 인터럽트)

PCINT23:0 (Mega 2560 계열에 새롭게 추가된 경우, 별도의 레지스터를 사용한다. 사용법이 다름.)

인터럽트 등록, 세팅은 어떤 인터럽트를 사용할지 결정하는 것이다. 그런데, 인터럽트 방식은 무엇을 말하는 것인가? 이는 신호에 대한 얘기이다. 우리가 디지털 신호를 얘기할 때, 신호가 가변될 수 있을 것이다. 그리고 우리는 인터럽트의 세팅에 따라서 인터럽트가 신호에 따라 실행될 때를 결정할 수 있다.

인터럽트의 trigger 방식

1. Level Trigger : High Level Trigger, Low Level Trigger (Event가 실제로 발생이 되는 원인 설정)  
    → 어떤 식으로 CPU가 High인지 Low인지 아냐면, 주기적으로 샘플링을 해서 확인한다. 만약, 스위치를 오랫동안 누르고 있다고 하자. (Active High Switch ) 이렇게 설정하면, 샘플링 주기가 High Level에 겹치는 모든 순간에 Interrupt가 발생한다. (그래서 이거보다는 Edge Trigger 사용)

2. Edge Trigger : Falling Edge, Rising Edge

    - Latch : Level Trigger
    - Flip Flop : Edge Trigger

똑같은 신호더라도, Trigger 방식에 따라서 Interrupt가 걸리는 조건과 횟수가 달라진다. 이를 Register를 통해 세팅해줄 수 있다.

---

EICRA(INT3:0) and EICRB (INT7:4)

이 것을 이 CPU만 보고 해야되는게 아니라, 나중에 또 다른 CPU나 Sensor, Module과 연결한다고 할 때, 보통 이벤트를 주고 데이터를 준다. 이벤트를 어떤 식으로 주냐를 같이 결정해야 하는 것이다. 어떤 트리거 방식을 쓸 것인지 논의를 해봐야 하고, 혹은 이미 결정되어있다면 매뉴얼을 보고 확인한다.

- PCINT

세 개로 나뉘어진다. PCI0 (PCINT7:0), PCI1 (PCINT15:8), PCI2(23:16).

PCMSK2, PCMSK1 and PCMSK0 Registers control which pins contribute to the pin change interrupts. PCINT는 기본적으로 toggle로 탐지한다.

PCINT23:0은 모두 비동기적으로 탐지된다. 즉, 어떻게 발생하더라도 모두 탐지할 수 있다는 것이다.

---

- 외부 인터럽트에 관한 Registers

- EICRA - External Interrupt Control (외부 인터럽트 제어)

    Register A

- EICRB - External Interrupt Control (외부 인터럽트 제어)

    Register B

- EIMSK - External Interrupt Mask Register  
    인터럽트 중에서 어떤 것을 쓸 지 체크하는 레지스터. 타이머 같은 곳에서도, 어떤 어떤 인터럽트 쓸 지 체크하는 레지스터가 존재한다. (인터럽트를 쓸지 말지 설정)
- EIFR - External Interrupt Flag Register  
    인터럽트가 발생을 하면, ISR을 수행을 해주지만, 그거보다 더 빨리 CPU가 알려주는 것이 바로 Flag Register이다. 인터럽트가 발생하면, 그 인터럽트에 해당하는 Flag가 올라간다. 자동으로 내려가지만, 강제로 내려줘도 된다. 만약 지금 ISR 안에 있는데, 다른 Interrupt가 실행이 되어있는 상태인지 체크하려면 Flag Register를 체크하면 된다.

---

- EICRA

ISC31 ISC30 ISC21 ISC20 ISC11 ISC10 ISC01 ISC00

ISC : Interrupt Sense Control

- Interrupt Sense Control(ISC)

ISCn1 ISCn0 : Description

0 0 : Low level Trigger

0 1 : 모든 Edge에서 Trigger 발생 (Switch 이용시, 눌렀을  때 한 번, 뗐을 때 한 번 발생)

1 0 : Falling Edge Trigger

1 1 : Rising Edge Trigger

Hysteresis(이력 현상) : 일정 전압 이상이면 High로 판단

Chattering : 물리적으로는 전압이 띄었는데, 전기적으로는 전하들이 남아서 곧바로 전압이 상승하지 못하는 현상… 만약 Level Trigger라면, 문제가 크게 없지만, 만약 Falling Edge라고 한다면, 인터럽트가 엄청나게 많이 발생하게 된다. 이를 방지하기 위해서, 보통 인터럽트가 수행된 뒤에 다시 사용될 때 일정 시간 동안은 다시 수행되지 않도록 한다.

- EICRB

ISC71 ISC70 ISC61 ISC60 ISC51 ISC50 ISC41 ISC40

다른 설정은 모두 같지만, 0 1 설정이 다르다. EICRB에서는 0 1이 Any Logical Change로 판단한다. 이 것은 레벨이 High에서 Low로 바뀌는 것을 판단한다. Toggle이 될 때마다 판단.

→ Edge Trigger랑 같은 것 아닌가요? 할 수도 있지만, 오래 누르고 있으면 다르다.

- EIMSK - External Interrupt Mask Register

내가 사용할 인터럽트를 세팅한다. 이는 하드웨어와 연동되어야 한다. 해당하는 외부 인터럽트 번호에 Interrupt를 Enable해주어야 한다.

- EIFR - External Interrupt Flag Register

인터럽트가 발생하면, CPU가 해당하는 비트를 set 해준다. 이후 인터럽트의 실행이 끝나면 비트가 clear된다. 경우에 따라서, 인터럽트가 굉장히 많을 때 CPU가 혼란이 올 때가 있다. 이런 상황에서는 코드를 작성해서 버그를 줄일 수 있도록 만들어 주어야 한다.

---

- PCINT Registers 

외부 인터럽트와 비슷한 기능을 하지만, 세팅을 한 묶음씩 한다. Toggle Driven이다. 즉, Edge Control이 불가능하며, Level이 바뀔 때마다 Interrupt가 발생한다. 만약 High → Low일 때만 인터럽트를 발생시키고 싶다면, S/W적으로 컨트롤 해주어야 한다.

- PCICR - Pin Change Interrupt Control Register

통째로, 8비트씩 Enable, Disable 해준다. 

\- \- \- \- \- PCIE2 PCIE1 PCIE0

PCICR에서 Enable 해주는 것은, Enable을 해준다는 것이고, PCIMSK에서 따로 Enable도 해주어야 함.

- PCIFR - Pin Change Interrupt Flag Register

인터럽트 발생 시, 통째로 set, clear 된다.

- PCIMSK2, PCIMSK1, PCIMSK0 - 이건 각각 8비트씩 존재한다.

---

인터럽트는 모두 ISR로 시작한다, Interrupt vector라는 것은, 해당하는 인터럽트의 이름이다.

SW1:PD2/INT2(19), SW2:PE4/INT4(2), SW3:PE5/INT5(19) 기본적으로 한 인터럽트 안에 들어가면, 다른 모든 인터럽트를 disable 해준다. cli(); 그 뒤, 인터럽트가 끝나고 ISR을 나갈 때 다시 enable 해준다. sei(); → 내가 원하는 위치에서 전체 인터럽트를 enable하고, disable할 수 있다. interrupt를 enable 하기 전에 동작을 하냐, enable하고 나서 동작을 하냐에 따라서 기능이 달라진다.

EIFR |= 0x04; // Interrupt 2번이 set된다. 원래는 CPU가 자동으로 올려줬다가, ISR에 들어가면 CPU가 내려가줘야 되는데, AVR 자체에 버그가 있어서 실행됐는데도 Flag가 내려가지 않는 경우가 있다. 이 때문에 강제로 Flag를 올려준다. 사용자가 Flag를 manual하게 올려주면 반드시 내려준다. 따라서 Flag를 set해주는 코드, 루틴을 추가한다. (ISR을 작성할 때 되도록 꼭 써야한다.)

\#include &lt;avr/interrupt.h&gt; 를 include 해줘야 한다. external이던, pin change던 include 해준다. 그렇다면 interrupt를 어떻게 setting해줄 것인가? (우리는 interrupt 2, 4, 5번을 쓴다)

EICRA~B는 Interrupt Sense Control을 해준다. 따라서, A, B를 모두 써야한다. 우리 코드에서는 인터럽트를 모두 Rising Edge로 만든다.

EICRA = 0x30;

EICRB = 0x0F;

EIMSK = 0x34;

EIFR = 0x34; // 루틴하게, 혹시 flag가 안내려가있는 경우에 대비해서 set해서 내려준다.

인터럽트는 입력으로 설정해줘야 한다.

DDRD 설정.

while 문은 작성할 필요 없고, Interrupt를 작성하면 된다. 앞서 polling 방식의 프로그램을 인터럽트 방식으로 바꾸어보자.

polling 방식에서는 인터럽트를 이용하여 task를 연속적으로 실행할 수 있게 됐다. 여기까지는 외부 인터럽트를 사용했다.

그런데, 우리가 또 다른 인터럽트인 PCINT가 있다. PCINT는 핀의 연결이 다르다.

SW1:PK0, SW2:PK1, SW3:PK3

PCICR은 bulk로, 8개씩 움직인다. 우리는 PK0, PK1, PK2를 쓰는데, 이것들은 PCINT 16~18이다.  
따라서 PCICR에서 2번비트 PCIE2에 속하므로, 이를 Enable 해주어야 쓸 수 있다.

PCMSK2에서 16~18, 즉 0번, 1번, 2번 비트를 Enable 해준다.

중요한 것은, 실제로 ISR은 PCINT2_vect라는 하나이지만, pin checking을 해서 어떤 동작을 할 지 체크한다는 것이다. 이를 들어오자마자 다시 체크해주어야 한다. 

---

과제에서는, 스위치1을 눌러서 shift를 하고 있는데, 다른 switch2를 눌렀으면 on/off를 하게 된다. 그리고 on/off가 끝나야 shift를 하게 됐다. 그런데 이걸 그냥, on/off가 끝나면 남은 shift가 실행 되지 않도록 만든다.
