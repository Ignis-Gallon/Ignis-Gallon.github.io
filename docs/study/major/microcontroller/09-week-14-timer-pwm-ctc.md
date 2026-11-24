---
title: "14주차: Timer, PWM, CTC 모드"
date: 2022-12-05
last_modified_at: 2022-12-05
category: major
tags: [마이크로컨트롤러응용, AVR, Timer, PWM, CTC]
---

# 14주차: Timer, PWM, CTC 모드

작성일 2022-12-05
{ .post-date }

## Timer 및 PWM(Pulse Width Modulation)

- 8bit timer, 16bit timer  
    ⇒ buffer size가 다르다. bit가 buffer의 사이즈이다. buffer의 사이즈가 다르면 무엇이 다른가?

    더 많은 클락을 셀 수 있다.

- 인터럽트의 종류는 크게 두 개가 있는데, `Overflow`, `OCR`이 있다.  
    ⇒ 결론적으로 말하면, `OCR`을 더 많이 쓴다.
- 8bit timer는 `0~255`까지 셀 수 있고, 255에서 더 들어오면 carry가 생겨서 0이 된다. 이 때 발생하는 인터럽트가 Overflow 인터럽트. (`Auto Reload`)

> 주파수 계산식  
> -Normal Mode  
> \- f = clock/(prescale \* TCNT)  
>
> 오버플로우 인터럽트를 Max(255)보다 작은 타임에서 사용하려면, 미리 값을 넣어놔야 한다. 예를 들어 클락이 1초에 한 개 들어오는데 10초에 걸리게 하고 싶다면, TCNT = 246을 넣어놓으면 될 것이다. (255 → 256 에서 오버플로우가 걸리므로. `256 - x = TCNT`)  
>
> 그런데, 이렇게 하면 매 번 초기화 될 때마다 TCNT = 246을 계속 넣어주어야 하므로, TCNT와 항상 값을 비교하며 오버플로우를 체크하는 OCR을 주로 사용한다.  
>
> 원하는 시간에 대한 TCNT or OCR 값 구하기.  
> \- 먼저 prescaler를 결정해서 들어오는 타이밍을 조절.

- OCR(Output Compare Register) : 항상 TCNT와 넣어놓은 값을 비교한다. 만약 10초에 인터럽트를 발생시키고 싶다면, `OCR = 10`을 넣어주면 된다.
- 필요할 때 16bit를 먼저 쓰고, 부족할 때 8bit를 쓰는 것이 좋다. 

## Registers

- TCCR
- TCNT
- OCR
- TIFR
- TIMSK
- TI…

짝수는 8bit, 홀수는 16bit Timer.

## Mode

- 실제로는 `CTC Mode`, `Fast PWM Mode`를 가장 많이 사용한다. 모드를 설정하려면 TCCR Register를 설정해주면 된다.
- 추가로, CTC Mode에는 `ICR`(Input Capture Register)가 존재하는데, OCR이랑 똑같다고 보면 된다. OCR은 TCNT와 값이 같아지면 인터럽트가 발생하는데, 모드마다 방식이 다르다. OCR에 값을 어떻게 줘야하냐는 것은 결국 한 클럭 들어오는데 시간이 얼마냐를 구하면 알 수 있다.
- prescaler : TCCR0B에서 CS02 CS01 CS00 을 통해서 결정해줄 수 있다. AVR Timer는 Timer를 stop하는 기능이 없으므로, prescaler 값을 000으로 설정해야 타이머가 멈춘다. prescaler를 1024로 설정하면 0.064초마다 1클락씩 들어오는데, 이를 이용해서 필요한 OCR값을 설정할 수 있다. 그런데. 딱 맞아 떨어지지는 않을 수도 있다.
- PWM : PWM을 이용하면 주기적인 Timeout을 만들 수 있는 것으로 보인다. 이벤트가 발생할 때마다 Timeout을 시키면, 주기적인 신호를 만들 수 있다. 이걸 CPU 바깥으로 내보내면 주기적인 모양의 신호를 내보낼 수 있게 된다. 주파수를 내보내는 것이다.
- TCCR1A에서 주파수를 내보낼지 안내보낼지를 결정하고, 사용할 수 있다.

## CTC Mode

- Top을 ICR, OCR을 결정할 수 있다. 우리가 원하는 식을 얻기 위해서는 OCRnA에 대한 식으로 수정해야 한다.

---

- Piezo Buzzer : 전기신호를 받아들여서 소리를 증폭해 들을 수 있는 가청주파수대로 바꿔준다. 동작 주파수는 1,000 - 2,500KHz,     80db ~ 100db
- 7414 register를 거쳐서 (conversion) 피에조 부저로 신호를 보내주면 된다.

OC3A 주파수 = 메인클럭 / (2 \* 분주비 \* (1 + ICR3))

OCR 인터럽트는 아웃풋으로 연결한 핀으로 나오게 된다.

---

> ISR 안에서 함수를 사용할 경우, 그 함수의 선언을 ISR 위에 올려주어야 한다. 안그러면 ISR 안에서 함수를 인식하지 못한다. 그러나, 직접 선언한 함수의 경우는 선언 위치가 크게 상관 없는듯 하다. 예를 들어 Printf() 함수는 tx_char() 함수가 Printf() 함수 선언의 아래 쪽에 와도 문제 없다.

```c
/*
 * micro14_1.c
 *
 * Created: 2022-11-29 오전 10:37:44
 * Author : JDK
 */ 

#define __DELAY_BACKWARD_COMPATIBLE__
#include <avr/io.h>
// #define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>
#include <avr/interrupt.h>
#include <stdio.h>

#define BAUD_9600 103
#define FREQ(x) (unsigned int) (16000000 / (2*8*(1+x)))
#define ON 1
#define OFF 0

int on_off = 0;

void Printf(char *fmt, ...) {
	va_list arg_ptr;
	uint8_t i, len;
	char sText[128];
	
	/* text buffer */
	for (i = 0; i < 128; i++) {
		sText[i] = 0;
	}
	
	va_start(arg_ptr, fmt);
	vsprintf(sText, fmt, arg_ptr);
	va_end(arg_ptr);
	
	len = strlen(sText);
	for (i = 0; i < len; i++) {
		tx_char(sText[i]);
	}
}

void tx_char(unsigned char txChar) {
	while(!(UCSR1A & (1 << UDRE1)));
	UDR1 = txChar;
}

void tx_str(unsigned char *txStr, int len) {

	int i;
	for (i = 0; i < len; i++) {
		tx_char(txStr[i]);
	}
}

// ISR을 위에 배치하면 오류가 발생하는 것으로 보인다.
ISR(INT0_vect) {
	_delay_ms(25);
	cli();
	on_off = ON;
	EIFR |= 0x01;
	sei();
	Printf("01 Clicked!\n");
}

ISR(INT1_vect) {
	_delay_ms(25);
	cli();
	on_off = OFF;
	EIFR |= 0x02;
	sei();
	Printf("02 Clicked!\n");
}

void play(unsigned int y) {
	
	// unsigned int x;
	DDRB |= 0x02;
	TCCR1A |= 0x04;
	TCCR1B |= 0x1A;
	ICR1 = y;
}

void stop(void) {
	
	TCCR1A = 0x00;
	TCCR1B = 0x00;
	TCNT1 = 0;
	ICR1 = 0;
	DDRB = ~0x02;
}

int main(void)
{
    cli();
	UBRR1H = (unsigned char) (BAUD_9600 >> 8);
	UBRR1L = (unsigned char) BAUD_9600;
	
	UCSR1A = 0x00;
	UCSR1B = (1 << TXEN1) | (1 << RXEN1);
	UCSR1B |= (1 << RXCIE1);
	UCSR1C = (1 << UCSZ11) | (1 << UCSZ10);
	
	DDRD = 0x00; // 외부 인터럽트 0, 1 INPUT으로 사용.
	DDRB = 0x80; // 7번 비트 활성화, 피에조에 output
	
	PORTD = 0x00;
	PORTB = 0x00;
	EICRA = 0x0A; // Falling Edge Trigger
	EIMSK = 0x03; // Interrupt Enable, 0, 1.
	EIFR |= 0x03;
	sei();
	
	Printf("System Enabled!\n");
    while (1) {
		
		if (on_off == ON) {
			play(2273);
		}
		else {
			stop();
		}
    }
}
```
