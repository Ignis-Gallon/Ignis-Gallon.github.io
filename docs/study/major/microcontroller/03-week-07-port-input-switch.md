---
title: "7주차: 포트 입력, 스위치 연결"
date: 2022-10-17
last_modified_at: 2022-10-18
category: major
tags: [마이크로컨트롤러응용, AVR, 포트 입력, 스위치, Polling]
---

# 7주차: 포트 입력, 스위치 연결

CPU를 기준으로 들어오는 것은 입력, 나가는 것은 출력이다.

마이크로컨트롤러(임베디드 시스템)의 장점은, H/W가 있으면 S/W를 어떤 걸 올리냐에 따라서 다양한 서비스를 가능하게 할 수 있다는 것이다. → S/W의 중요성  
똑같은 LED를 사용하더라도, LED를 어떻게 구성하고 S/W를 어떻게 구성하느냐에 따라서 확연히 다른 동작이 구현될 수 있다.

밖에서 딴 사람이 High나 Low로 만들고 있을 때, CPU 입장에서 지금 연결된 선이 High인지 Low인지 확인을 하고 싶을 때 포트 입력을 사용한다. 디지털의 기본은 Switch이다. Switch를 누르면 연결이 돼서 1이고 누르지 않으면 0이다. 아주 간단하게 선이랑 연결해서 스위치를 누르면 1, 떼면 0.

- LED 출력장치

    → PC에서 stdin은 keyboard(mouse는 보조 입력 장치이다.) 임베디드시스템은 stdin이 없다. 동작을 바꾸고 싶으면 Hard Coding을 해야한다. 그런데, 포트 입력이 가능해지면 입력으로 선택적으로 실행하도록 만들면 되기 때문에, 포트 입력은 프로그래밍을 유연하게 만들어주는 역할을 한다. 추가적으로, 선택을 할 수 있어야만 어떤 시스템을 만들 수 있다.

    ex) 자판기, 내비게이션 등

임베디드시스템은 input으로 받아들일 수 있는 장치가 굉장히 다양하다. 이는 기존의 컴퓨터와 다른 점이며, 그런 입력에 의해서 동작이 이루어진다.

- I/O : 모든 시스템에는 적어도 하나 이상의 입력 장치가 있고, 하나 이상의 출력 장치가 존재.

포트 입력을 하려면, 외부에서 임의로 High or Low로 만들어주어야 한다. 가장 쉬운 방법은 Switch를 연결하는 것이다. 여기서는 Switch 세 개를 사용한다.

> 주의! 스위치는 반드시 위 아래가 분리되도록 연결한다.  
> (위 아래 간격을 둬서 꽂아줘야한다.)

49 (PL0)  
48 (PL1)  
47 (PL2)

PL0,1,2는 XIOL의 47, 48, 49핀으로 연결

- CPU에서 포트 입력을 어떻게 할 것인가?

→ DDR을 설정해야한다. 입력을 받을 것이므로, DDR을 0으로 설정해준다.  
default가 0이라서 기본이 입력이기는 하지만, 초기화를 다시 진행해준다.

e.g) DDRL = ~(0x07); // 상위가 1이 되지만, 그건 그때가서 바꾸면된다.

- PINx Register를 사용한다. 단순히 값을 읽어오면 안되고, 해당하는 비트의 값이 1인지 0인지를 확인하면서 읽어줘야 한다. 

ex) led3 = (PINL & 0x02); // 0000 0010

→ 변수에 값을 받던지, 조건에 바로 넣어두던지 하면 된다. (값을 확인할 수 있음)

- shift를 해서 제어를 해보자. 부분 동작 shift
- 강의 코드

```c

/*
 * micro7_1.c
 *
 * Created: 2022-10-17 오전 9:41:50
 * Author : JDK
 */ 

#define __DELAY_BACKWARD_COMPATIBLE__
#include<avr/io.h>
#define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>

int main(void)
{
  DDRC = 0xFF;
	DDRL = ~(0x01); // Switch는 PL0만 이용한다.
	
	// LED가 켜지지 않도록 초기값을 0xFF로 설정해준다.
	PORTC = 0xFF;
	
	unsigned char in;
	unsigned char val=0x01;
	
	// ~val을 해서 0번 비트만 켜지도록 만든다.
	PORTC = ~val;
	while (1) {
		
		
		in = (PINL & 0x01); 
		// 우리가 사용하는 Switch는 구조상 누르면 연결이 빠져서
		// Ground로 전달되는 구조이다. 따라서, 눌러져 있는 동안, 을 체크하려면
		// !in을 활용해야 한다.
		if (!in) {
			
			if (val&0x80) {
				val = 0x01;
			}	
			else {
				val = val << 1;
			}
			PORTC = ~val;
			_delay_ms(300);
		}
		
		// 그런데, 코드를 이렇게 구성하면
		// 계속 누르고 있으면 아마도 Shift가 계속 될 것이다.
	}
}
```

- 나의 코드(초기 상태에 LED가 모두 꺼져있는 버전)

```c
/*
 * micro7_2.c
 *
 * Created: 2022-10-17 오후 2:59:16
 * Author : JDK
 */ 

#define __DELAY_BACKWARD_COMPATIBLE__
#include<avr/io.h>
#define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>

int main(void)
{
	// 현재 어느 if 문에 들어가 있는지
	// 체크하기 위한 flag LED로 PORTA의 0번 비트 선택.
	DDRA=0x01;
	PORTA=0xFF;
	
    DDRC=0xFF;
	DDRL=~(0x01);
	
	unsigned char in = 0x00;
	unsigned char value = 0xFF;
	int flag = 0;
	// 올바른 shifting을 구현하기 위해 필요한
	// flag 변수
	
	PORTC = value;
	
	while (1) {
		
		in = (PINL & 0x01);
		
		if (!in) { // 스위치가 눌렸을 때
			
			PORTA=0x00;
			
			if (flag == 1) {
				continue;
			}
			else if (flag == 0) { 

				flag = 1;	
			
				if (value == 0xFF) {
					// 초기 상태
					value = ~(0x01);
				} 
				else if (value == 0x7F) {
					value = ~(0x01);
				} 
				else {
						// 1111 1110
						// 0000 0001 compliment
						// 0000 0010 compliment again
						// 1111 1101 implmentes shifting
					value = ~((~value) << 1);
				}
				
				PORTC = value;
				
				// 의도치 않은 동작을 방지하기 위한
				// 짧은 시간의 delay를 임의로 설정
				// why? switch가 정교하지 않기 때문에
				// 눌렀다가 떼는 과정에서 의도치 않게
				// on/off가 다시 발생하게 될 수 있음.
				_delay_ms(300);
			}
		}
		else if (in) { // 스위치가 눌리지 않았을 때
			
			PORTA=0x01;
			
			if (flag == 1) {
				// 사용자가 스위치를 눌렀다가 뗀 경우,
				// flag를 0으로 만들어준다.
				
				flag = 0;
			}
			else if (flag == 0) {
				// 아무 것도 아닌 상태.
				// 스위치를 누르지도 않았고, 눌려있던 상태도 아님.
				continue;
			}
		}
	}
}
```

- 최소화 버전

```c
#define __DELAY_BACKWARD_COMPATIBLE__
#include<avr/io.h>
#define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>

int main(void)
{

	DDRA=0x01;
	PORTA=0xFF;
	
    DDRC=0xFF;
	DDRL=~(0x01);
	
	unsigned char in = 0x00;
	unsigned char value = 0xFF;
	int flag = 0;
	
	PORTC = value;
	
	while (1) {
		
		in = (PINL & 0x01);
		
		if (!in) { // 스위치가 눌렸을 때
			
			PORTA=0x00;

			if (flag == 0) { 

				flag = 1;	
			
				if (value == 0xFF || value == 0x7F) {
					// 초기 상태
					value = ~(0x01);
				} 
				else {
					value = ~((~value) << 1);
				}
				
				PORTC = value;
				_delay_ms(300);
			}
		}
		else if (in) { // 스위치가 눌리지 않았을 때
			
			PORTA=0x01;
			
			if (flag == 1) {	
				flag = 0;
			}
		}
	}
}
```

---

포트 입력을 할 수 있게 되면서 프로그램을 사용자의 의도에 맞게 프로그램의 흐름을 바꿀 수 있게 되었다. CPU 입장에서 보면, 사용자가 언제 어떤 스위치를 누를지 알 수 없다. 그래서 전체적인 코드의 아키텍처가 앞의 코드처럼 만들어졌다. 즉, 현재 상태가 어떤지 체크하는 부분이 무한 루프에서 계속 돌고있다는 것이다. 앞의 코드에서는 스위치가 눌렸으면 하나씩 shift 해줬는데 이 동작의 가장 기본은 눌렸는지 체크하는 것이다. 

2번 스위치를 사용한다. 1번 스위치의 역할은 똑같은데, 2번 스위치를 누르면 얘는 반대로 가게 된다. 

각 task를 구현하는 것은 그렇게 어렵지 않다. 핵심적으로 봐야하는 것은, 현재 어떤 입력이 들어오고 있는지를 체크해주는 동작을 구현해주어야 한다.

- Polling (폴링) : 계속적으로 해당 이벤트가 발생했는지, 확인하고 발생한 경우 해당 루틴 수행.  
    → 문제는, 이렇게 구현하면 놓치는 경우가 발생할 수도 있다.

> [실습 과제]  
>
> 네 개의 스위치와 LED를 아래와 같이 연결  
> \- LED: PA4…PA7 상위 비트, PC0…PC3 하위 비트  
> \- PL0..2 은 세 개의 스위치로 연결  
>
> Action  
> \- 1번 스위치 : LED SHIFT 함수 실행 // 왕복으로 3회  
> \- 2번 스위치 : LED ALL ON/OFF 함수 실행 // 전체 3회  
> \- 3번 스위치 : LED ALTERNATING // 각 세트별 3회  
>
> 스위치(DDRL)의 누르지 않았을 때, default 값은 1이다.

현재 코드에서 문제점이 있다면, 하나의 동작을 시작하면 동작이 끝날 때까지 다른 동작을 수행할 수 없다는 것이다. 즉, polling을 통해서 특정 switch가 on되어서 하나의 함수(task)를 수행하게 되면, 다른 동작을 수행할 수 없는 상태가 된다.  
→ 이를 개선하려면, Interrupt를 이용하면 된다.

> [문제 정의]  
>
> 첫 번째 Switch를 누른 동안에도, 다른 스위치를 누르면 그 스위치를 인식해서 다른 동작을 수행하도록 하는 기능의 시스템을 구현하고자 한다. 이를 인터럽트로써 구현할 수 있다.

CPU는 기본적으로, 순차적으로 일을 한다. 메모리에 저장된 프로그램을 한 라인씩 가져와서 실행한다. (fetch) 만약, 프로그램이 순차적으로 실행되지 않는다고 하면, 랜덤으로 실행된다고 하면, 우리는 프로그램을 코딩할 수 없다. 그렇지 않으면 우리의 의도대로 프로그램을 구성할 수 없게 되는 것이다. 그래서 CPU는 한 라인씩 메모리에서 프로그램을 가져와서 sequential하게 수행하고 다음 라인을 또 가져오고… 이런 식으로 수행한다. 

이런 상황임에도 불구하고 만약 인터럽트가 발생하게 되면, 현재 어떤 상황이던지 인터럽트가 해야될 일인 ISR을 실행해서 요구되는 task를 수행하도록 만든다.

PC(Program Counter) : CPU가 다음으로 실행할 명령어의 주소를 기억한다. (처음 실행하는 0번지부터 가리키고, CPU가 fetch를 하면 바로 다음 주소를 가리키게 된다.)

만약 103번이 수행되고, 다음으로 104번이 수행돼야 하는 시점일 때 인터럽트가 발생한다면 PC값을 stack에 push하고 PC를 ISR의 첫 번째 코드를 가리키게 만든다. 이렇게 되면 CPU는 PC 값을 보고 그 위치로 가서 코드를 fetch해서 실행한다. 갖고 오는 동안에도 PC는 값이 하나 씩 증가하게 되고, ISR이 끝나게 되면 stack에서 pop을 해서 PC의 값을 원래 값을 가리키게 만든다. 이렇게 되면 다시 CPU가 아무일도 없었다는 듯이 원래 실행할 라인이었던 104번 라인을 수행하게 된다.

- Interrupt란?

프로그램 실행 중에 CPU의 현재 처리 순서를 중단시키고 다른 동작을 수행하도록 하는 것.

외부로부터 인터럽트 요구가 들어오면,  
\- CPU는 원래의 프로그램 수행을 중단하고,  
\- 요구된 인터럽트를 위한 서비스 프로그램을 먼저 수행

인터럽트 서비스 루틴(ISR)  
\- 인터럽트를 처리하기 위하여 수행하는 프로그램 루틴. 그러나, 다른 함수처럼 function call로 수행되지는 않으며, CPU가 interrupt를 catch해서 ISR을 수행시킨다.

만약, ISR 안에서도 코드가 너무 길어지면, Interrupt끼리 꼬이는 경우가 발생하게 된다. polling 방식에서 아예 불가능한 것은 아니지만, 다른 함수가 실행되는 과정에서 다른 함수가 실행하게 되도록 만들려면 함수 안에서 다른 Switch가 눌렸는지 체크하는 코드를 넣어서 마치 인터럽트처럼 구현할 수는 있다. 하지만, 실제로 실행하는 것이 엉망이 되고 stack이 꼬이게 된다.

→ 실행 중에 다른 것들을 너무 많이 고려한 경우. 이런 것은 중앙에서 누군가가 조율해 주어야 한다.  
(따라서, ISR을 너무 길게 짜면 안된다. 하지만, ISR 안에서 다른 인터럽트를 Blocking 한다면, polling과 차이가 없어진다. 하지만 또, ISR이 너무 길어지면 인터럽트가 겹쳐서 수행되면서 동작이 꼬이게 된다. memory가 큰 경우에는 문제가 없지만, AVR architecture나 Embedded System에서는 stack에 pointer를 저장해두기 때문에 stack overflow가 발생할 수도 있다. 그러나, stack이 overflow되는지 안되는지 체크할 수 있는 프로세서는 적어도 32bit이상이다. 이를 관리해주는 것을 Memory Management Unit이라고 하는데, AVR에는 MMU가 없어서 stack이 overflow 되어서 올라가는 것을 관리해주지 못한다. 그래서 계속해서 올라가면, 다른 메모리 영역을 침범하여 기존에 존재하는 변수의 값을 바꿀 수도 있으므로 프로그램이 망가질수도 있다.)
