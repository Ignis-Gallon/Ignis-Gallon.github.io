---
title: "13주차: UART 커맨드 프로토콜, ADC"
date: 2022-11-28
last_modified_at: 2022-12-07
category: major
tags: [마이크로컨트롤러응용, AVR, UART, FSM, ADC]
---

# 13주차: UART 커맨드 프로토콜, ADC

지금까지는 String을 보내서 했는데, 이번에는 미리 맞춰놓은 Command를 보내도록 해보자. 미리 명령어를 만들고 보낸다고 생각.  
⇒ 이런걸 하려면, 사전에 규약이 정해져 있어야 함. 이를 프로토콜이라고 하는데, 프로토콜의 요소에는 1) 데이터 타입(hex or ASCII), 2) len(FIXED or Variable), 3) Start delimiter & Termination Delimiter(STX, 0x02 / ETX, 0x03)

- Format

    \| STX(0x02) \| command(1byte) \| ETX(0x03) \|

- FSM for Rx (Finite State Machine)

<!-- -->

1. Normal (Event: STX, Action: Nothing)
2. Data Reception(Event: Data, Action: store)
3. Termination  
    (Event: Not ETX, Action : Error)  
    (Event: ETX, Action: Set Flag → Store하고 있던 Command를 수행…)

<!-- -->

- 가장 좋은 것은, FSM에서 state를 구분하는 전역 변수를 만들어서 동작을 나누는 것이 가장 좋다.
- My Code

```c
// Loopback

#define __DELAY_BACKWARD_COMPATIBLE__
#include <avr/io.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>
#include <avr/interrupt.h>

#define MAX_TXBUF_SIZE 128
#define BAUD_9600 103
// #define BAUD_115200 8

void LED_ALL_ON_OFF(int _cnt, int _interval);
void LED_ALTERNATING(unsigned char _value, int _cnt, int _interval);
void LED_SHIFT(int _cnt, int _interval);

// FSM 상에서 state를 구분하는 변수.
// int state = 0;
int rx_started = 0;
int rx_completed = 0;
unsigned char buf = 0;

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

ISR (INT4_vect) {
	//cli();
	//_delay_ms(20); // 채터링 방지
	//unsigned char *tmp;
	//tmp = malloc(30);
	//memset(tmp, '\0', 30); // 30 바이트의 공간을 null로 채운다.
	//tmp = "INT4 PUSHED\n";
	//buf_len = strlen(tmp);
	//strncpy(tx_buf, tmp, buf_len); // tx_buf에 tmp를 복사해 넣는다. 길이는 buf_len.
	//EIFR |= 0x10;
	//sei();
}

ISR (INT5_vect) {
	//cli();
	//_delay_ms(20);
	//unsigned char *tmp;
	//tmp = malloc(30);
	//memset(tmp, '\0', 30);
	//tmp = "INT5 PUSHED\n";
	//buf_len = strlen(tmp);
	//strncpy(tx_buf, tmp, buf_len);
	//EIFR |= 0x20;
	//sei();
}

// UART 수신은 인터럽트를 사용한다.
// UART 송신은 인터럽트를 사용하지 않는다.
ISR(USART1_RX_vect) {
	unsigned char _usart_rcv = 0;
	_usart_rcv = UDR1;
	
	if (rx_started == 0 && _usart_rcv == 0x02) {
		rx_started = 1;
		sei();
		return;
	} else if (rx_started == 0 && _usart_rcv != 0x02) {
		sei();
		return;
	}
	
	if (rx_started == 1 && rx_completed == 0) {
		buf = _usart_rcv;
		rx_completed = 1;
		sei();
		return;
	}
	
	if (rx_started == 1 && rx_completed == 1) {
		if (_usart_rcv == 0x03) {
			// 통신 성공...
			command(buf);
			
			// 여기서 command를 부르지 않고, flag를 set해준 뒤에
			// main에서 처리해도 되기는 한다.
		}
	
		buf = 0;
		rx_started = 0;
		rx_completed = 0;
		sei();
		return;
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

void command(unsigned char buff) {

	if (buf == 0x01) {
		// tx_str(tx_buf, buf_idx);
		LED_ALL_ON_OFF(3, 100);
	}
	else if (buf == 0x02) {
		// tx_str(tx_buf, buf_idx);
		LED_ALTERNATING(0xAA, 3, 200);
		LED_ALTERNATING(0xF0, 3, 200);

	}
	else if (buf == 0x04) {
		// tx_str(tx_buf, buf_idx);
		LED_SHIFT(3, 50);
	}
	else {
		Printf("Error");
	}
}

int main(void)
{
	unsigned char rx_char = 0;
	cli();
	// DDRE &= ~(0x30); // 버튼을 사용하지 않기 때문에 이 프로젝트에서는 의미 없음.
	DDRC = 0xFF; // PORTC에서 모든 LED를 output으로 제어하기 위해 DDRC = 0xFF;
	PORTC = 0xFF; // LED에 불이 켜지지 않도록 하기 위해 PORTC = 0xFF로 설정.
	
	EICRB = 0xAA; // 외부인터럽트 4,5,6,7번을 사용하는 설정으로 보인다.
	EIMSK = 0x30; // 외부인터럽트 4,5번 마스크
	EIFR |= 0x30; // 외부인터럽트 플래그 4,5번 set to clear
	
	UBRR1H = (unsigned char) (BAUD_9600 >> 8);
	UBRR1L = (unsigned char) BAUD_9600;
	UCSR1A = 0x00;
	UCSR1B = (1 << TXEN1) | (1 << RXEN1); // Rx, Tx Enabled
	UCSR1B |= (1 << RXCIE1);
	UCSR1C = (1 << UCSZ11) | (1 << UCSZ10); // Data Set 크기 8로 설정
	// parity bit는 기본 설정이 0
	// stop bit는 기본 설정이 1
	sei();
	
	Printf("*************************************************\n\n");
	Printf("AVR SYSTEM init OK...\n");
	Printf("Clock Speed = %ld\n", FOSC); // AVR에서는 int가 두 바이트라서 천 육백만을 담지 못함. ld사용.
	Printf("Interrupts = 0x%x\n\n", EIMSK);
	Printf("*************************************************\n");
	
	while (1)
	{
	}
	
	return 0;
}

void LED_ALL_ON_OFF(int _cnt, int _interval) {
	
	for (int i = 0; i < _cnt; i++) {
		PORTC = 0x00;
		_delay_ms(_interval);
		PORTC = 0xFF;
		_delay_ms(_interval);
	}
	
	PORTC = 0xFF;
}

void LED_ALTERNATING(unsigned char _value, int _cnt, int _interval) {
	
	for (int i = 0; i < _cnt; i++) {
		PORTC = (0xFF & _value);
		_delay_ms(_interval);
		
		PORTC = ~(0xFF & _value);
		_delay_ms(_interval);
	}
	
	PORTC = 0xFF;
}

void LED_SHIFT(int _cnt, int _interval) {
	
	PORTC = ~(0x01);
	_delay_ms(_interval);
	
	for (int i = 0; i < _cnt; i++) {
		
		for (int j = 1; j < 8; j++) {
			PORTC = ~(0x01 << j);
			_delay_ms(_interval);
		}

		for (int j = 1; j < 8; j++) {
			PORTC = ~(0x80 >> j);
			_delay_ms(_interval);
		}
	}
	
	PORTC = 0xFF;
}
```

- Prof’s code

```c
// Loopback

#define __DELAY_BACKWARD_COMPATIBLE__
#include <avr/io.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>
#include <avr/interrupt.h>

#define MAX_TXBUF_SIZE 128
#define BAUD_9600 103
// #define BAUD_115200 8
#define LEDONOFF 0x01
#define LEDALT 0x02
#define LEDSHIFT 0x04
#define STX 0x02
#define ETX 0x03

int decode = 0; // 0: normal, 1: data reception, 2: termination
uint8_t _command = 0;

void LED_ALL_ON_OFF(int _cnt, int _interval);
void LED_ALTERNATING(unsigned char _value, int _cnt, int _interval);
void LED_SHIFT(int _cnt, int _interval);

// FSM 상에서 state를 구분하는 변수.
// int state = 0;
int rx_started = 0;
int rx_completed = 0;
int sint_flag = 0;
unsigned char buf = 0;

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

ISR (INT4_vect) {
	//cli();
	//_delay_ms(20); // 채터링 방지
	//unsigned char *tmp;
	//tmp = malloc(30);
	//memset(tmp, '\0', 30); // 30 바이트의 공간을 null로 채운다.
	//tmp = "INT4 PUSHED\n";
	//buf_len = strlen(tmp);
	//strncpy(tx_buf, tmp, buf_len); // tx_buf에 tmp를 복사해 넣는다. 길이는 buf_len.
	//EIFR |= 0x10;
	//sei();
}

ISR (INT5_vect) {
	//cli();
	//_delay_ms(20);
	//unsigned char *tmp;
	//tmp = malloc(30);
	//memset(tmp, '\0', 30);
	//tmp = "INT5 PUSHED\n";
	//buf_len = strlen(tmp);
	//strncpy(tx_buf, tmp, buf_len);
	//EIFR |= 0x20;
	//sei();
}

// UART 수신은 인터럽트를 사용한다.
// UART 송신은 인터럽트를 사용하지 않는다.
ISR(USART1_RX_vect) {
	unsigned char _usart_rcv = 0;
	_usart_rcv = UDR1;
	
	if (decode == 0 && _usart_rcv == STX) {
		decode = 1; // 다음 state로 넘어가기 위해 필요. Action은 없음.
	}
	else if (decode == 1) {
		_command = _usart_rcv;
		decode = 2;
	}
	else if (decode == 2) {
		if(_usart_rcv == ETX) {
			sint_flag = 1;
		}
		else {
			Printf("RX error\n");
		}
		
		decode = 0;
	}
	
	sei();
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

void hex_command(unsigned char cd) {

	if (cd == LEDONOFF) {
		// tx_str(tx_buf, buf_idx);
		LED_ALL_ON_OFF(3, 100);
	}
	else if (cd == LEDALT) {
		// tx_str(tx_buf, buf_idx);
		LED_ALTERNATING(0xAA, 3, 500);
		LED_ALTERNATING(0xF0, 3, 500);
		LED_ALTERNATING(0x3C, 3, 500);

	}
	else if (cd == LEDSHIFT) {
		// tx_str(tx_buf, buf_idx);
		LED_SHIFT(3, 50);
	}
	else {
		Printf("Error");
	}
}

int main(void)
{
	unsigned char rx_char = 0;
	cli();
	// DDRE &= ~(0x30); // 버튼을 사용하지 않기 때문에 이 프로젝트에서는 의미 없음.
	DDRC = 0xFF; // PORTC에서 모든 LED를 output으로 제어하기 위해 DDRC = 0xFF;
	PORTC = 0xFF; // LED에 불이 켜지지 않도록 하기 위해 PORTC = 0xFF로 설정.
	
	EICRB = 0xAA; // 외부인터럽트 4,5,6,7번을 사용하는 설정으로 보인다.
	EIMSK = 0x30; // 외부인터럽트 4,5번 마스크
	EIFR |= 0x30; // 외부인터럽트 플래그 4,5번 set to clear
	
	UBRR1H = (unsigned char) (BAUD_9600 >> 8);
	UBRR1L = (unsigned char) BAUD_9600;
	UCSR1A = 0x00;
	UCSR1B = (1 << TXEN1) | (1 << RXEN1); // Rx, Tx Enabled
	UCSR1B |= (1 << RXCIE1);
	UCSR1C = (1 << UCSZ11) | (1 << UCSZ10); // Data Set 크기 8로 설정
	// parity bit는 기본 설정이 0
	// stop bit는 기본 설정이 1
	sei();
	
	Printf("*************************************************\n\n");
	Printf("AVR SYSTEM init OK...\n");
	Printf("Clock Speed = %ld\n", FOSC); // AVR에서는 int가 두 바이트라서 천 육백만을 담지 못함. ld사용.
	Printf("Interrupts = 0x%x\n\n", EIMSK);
	Printf("*************************************************\n");
	
	while (1)
	{
		if(sint_flag) {
			hex_command(_command);
			sint_flag = 0;
			_command = 0;
		}
	}
	
	return 0;
}

void LED_ALL_ON_OFF(int _cnt, int _interval) {
	
	for (int i = 0; i < _cnt; i++) {
		PORTC = 0x00;
		_delay_ms(_interval);
		PORTC = 0xFF;
		_delay_ms(_interval);
	}
	
	PORTC = 0xFF;
}

void LED_ALTERNATING(unsigned char _value, int _cnt, int _interval) {
	
	for (int i = 0; i < _cnt; i++) {
		PORTC = (0xFF & _value);
		_delay_ms(_interval);
		
		PORTC = ~(0xFF & _value);
		_delay_ms(_interval);
	}
	
	PORTC = 0xFF;
}

void LED_SHIFT(int _cnt, int _interval) {
	
	PORTC = ~(0x01);
	_delay_ms(_interval);
	
	for (int i = 0; i < _cnt; i++) {
		
		for (int j = 1; j < 8; j++) {
			PORTC = ~(0x01 << j);
			_delay_ms(_interval);
		}

		for (int j = 1; j < 8; j++) {
			PORTC = ~(0x80 >> j);
			_delay_ms(_interval);
		}
	}
	
	PORTC = 0xFF;
}
```

---

- ADC (Analog to Digital Converter)

10-bit Resolution : resolution이란, 끊어서 볼 수 있는 레벨. 0~1023 까지의 레벨로 끊어서 볼 수 있다는 뜻이다.

- ADC에서는 16개의 채널이 있다. 16개의 포트 중 하나를 골라서 사용하면 된다. 고르는 것은 ADMUX를 사용한다. 
- 기준점은 ADMUX register의 REFS1, REFS0 를 선택해서 고른다. 얘를 보통 0, 1로 주는데, 이것이 기본 5V를 의미한다. 
- ADLAR : ADC8:15 까지 선택하려면, 해당 비트를 set해주고, MUX에 값을 채워준다.
- MUX4:0 : 몇 번 ADC를 고를지 선택해준다.
- ADCSRA Register

Bit 7 - ADEN : ADC Enable

Bit 6 - ADC Start Conversion

Bit 5 - ADC Auto Trigger Enable

Bit 4 - ADIF : ADC Interrupt Flag

Bit 5 - ADIE

prescaler 주기 : 값을 읽어오는 주기를 설정하는 것으로 보인다.

- ADC 값을 보려면 보통, 뭔가에 의해서 변해야 되기 때문에 가변 저항을 이용해서 저항을 바꾸면서 값을 실시간으로 읽어본다.
- 그 값에 따라서 LED가 어떻게 켜지는지 봐본다.

---

- 가변 저항을 이용한 ADC 제어

(ADC0)PF0

가변 저항 : VCC PIN GND (VCC, GND의 위치는 상관 없음. 편하게 꽂기. 가운데 PIN을 PF0로 연결)

- 압력 센서도 똑같음. 코드는 똑같고 하드웨어 연결만 조금 다름

```c
/*
 * micro13_3.c
 *
 * Created: 2022-11-29 오전 10:37:44
 * Author : JDK
 */ 

#define __DELAY_BACKWARD_COMPATIBLE__
// #define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>
#include <avr/interrupt.h>
#include <stdio.h>
#define BAUD_9600 103

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

unsigned int GetADCData(unsigned char aIn) {
	volatile unsigned int result;
	ADMUX = aIn; // Channel Selection (0~7)
	ADMUX |= (1 << REFS0);
	ADCSRA = (1 << ADEN) | (1 << ADPS2) | (1 << ADPS1) | (1 << ADPS0);
	// ADC Enable, ADC prescaler : 128
	ADCSRA |= (1 << ADSC); // ADC Start conversion. 

	while(!(ADCSRA & (1 << ADIF))); // ADIF is set when an ADC Completes
	_delay_us(300);
	
	result = ADCL + (ADCH << 8);
	ADCSRA = 0x00;
	
	return result;
}

int main(void)
{
	UBRR1H = (unsigned char) (BAUD_9600 >> 8);
	UBRR1L = (unsigned char) BAUD_9600;
	UCSR1B = (1 << TXEN1) | (1<< RXEN1);
	UCSR1C = (1 << UCSZ11) | (1 << UCSZ10);
    
    while (1) {
		
		_delay_ms(500);
		Printf("ADC: %d\n", GetADCData(0));
		
    }
}
```
