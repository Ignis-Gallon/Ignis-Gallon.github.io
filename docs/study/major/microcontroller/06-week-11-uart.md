---
title: "11주차: UART 통신"
date: 2022-11-14
last_modified_at: 2022-11-21
category: major
tags: [마이크로컨트롤러응용, AVR, UART, 시리얼 통신]
---

# 11주차: UART 통신

## UART 통신

- 일반적으로 Serial 통신이라고 하면, UART 통신을 말한다.   
    UART(Universal Asynchronous Receiver and Transmitter)
- Synchronous 통신은 클럭을 같이 전달해주어야 한다. 하지만 Asynchronous 통신은 데이터만 전달받기 때문에 클럭을 전달받는 라인이 필요없다. 거의 대부분의 통신은 Asynchronous이다. 

임베디드 개발할 때 가장먼저 하는 것이 먼저 포트를 뚫고, UART 통신을 뚫은 다음에 개발을 시작한다. 왜 UART를 뚫냐면, 임베디드 시스템은 그 자체로 디버깅이 불가능하기 때문에 UART를 이용해 디버깅을 진행해야 한다. mega 2560은 세 개의 UART 포트가 존재한다.

- RS232(Recommended Standard, 표준) : UART는 보통 1m ~ 1.5m 이내에서 하는 시리얼 통신인데 그것을 10m 이상으로 늘려줄 수 있는 표준 통신 방법. (pumping, 전압을 증가시켜서 멀리 전달될 수 있도록 한다.) 일반적인 UART to UART은 TTL 레벨 (5v)을 사용한다.

참고 : RS485, RS422도 있다. 이건 one to many converter.

---

Synchronous 통신은 Clock 라인, Data 라인이 연결되어 있을 때 Clock 하나당 데이터 하나 씩을 내보낸다. Slave는 Master가 보내는 Clock에 종속되기 때문에 S/W와 H/W가 굉장히 심플해진다. 

Asynchronous 통신은 언제 통신이 시작될 지를 모른다. 이를 알려주기 위해 데이터의 시작을 알려는 Start Condition이 필요하다. 기본적으로 서로 High로 잡고 있다가 Low로 내리면서 시작한다. 샘플링을 얼마나 자주하냐에 대한 정보를 Sampling rate라고 한다. 그래서 통신 속도를 서로 사전에 맞춰야 하며, 추가적으로 몇 가지를 맞춰줘야 한다. (Baud rate, 통신 속도)

- Tx, Rx가 약속해야 하는 것들

<!-- -->

1. 통신 속도
2. 스톱비트
3. 패리티
4. 데이터길이

<!-- -->

- 데이터 길이 : 5, 6, 7, 8, 9 bits
- 패리티 비트 : Error Detection (ASCII가 7bit인데, 여기다가 1비트를 써서 Parity를 표현한다.)  
    → None(아예 없음), Even Parity(둘다 even[1]), Odd Parity(둘다 odd)  
    보낸 쪽에서의 1의 개수와 받은 쪽의 1의 개수를 비교하여 even인지 odd인지 확인한다.
- 스톱 비트 : normal이 standby high이므로, low로 계속 유지한다. 

Tx가 High로 잡고있다가 데이터를 보낼 때 Low로 내린다. Low를 일정시간 유지하다가(노이즈 방지) 데이터 전송을 시작한다. Rx는 High→Low가 되는 순간 약속한 상태인지 확인하여 전송 시작인지 확인한다. Tx는 사전에 약속했던 길이만큼 데이터를 전송한다. 그 다음 parity bit를 보낸다. (설정되어 있다면) 1의 개수를 체크한다. 이상이 없다면 이제 이 데이터를 쓸 건데, 정말 써도 되는지 확인하려면 정해진 스톱 비트를 활용한다. 만약 스톱 비트가 2 bit라면, 그 시간동안 low로 내려놓는다. 

UART 통신 설정에 사용되는 Register들

- USARTn I/O Data Register - UDRn
- USART Control and Status Register A - UCSRnA
- USART Control and Status Register B - UCSRnB
- USART Control and Status Register C - UCSRnC
- USART Baud Rate Register - UBRRnL, UBRRnH

한 프레임에서 전달받는 데이터는 UDRn 이라는 Register에 들어간다. UDR에 쓰고 받을 수 있다. UDR은 하나는 Read로 되어있고, 하나는 Write가 있는데 쓰면 보내는 거고, 읽으면 받는 것이다. (Full-duplex, 논리상 동시에 주고 받을 수 있다. 하지만, CPU가 한 개이기 때문에 한 번에 한 개의 동작만 구현할 수 있다.)

- UCSRnA

Bit 7 - RXCn : USART Receive Complete  
이 이벤트가 떴을 때만 UDR을 읽어서 데이터를 가져올 수 있다.

Bit 6 - TXCn : USART Transmit Complete  
보낼 때 사용하는 비트이지만, 거의 사용하지 않는다.

Bit 5 - UDREn : USART Data Register Empty  
UDR이 현재 비어있는지 체크한다. (데이터를 보내기 전에는 항상 empty인지 체크한다.)

Bit 4 - FEn : Frame Error  
Frame에 오류가 난 경우

Bit 3 - DORn : Data OverRun  
stop bit가 나와야 되는데 나오지 않은 경우

Bit 2 - UPEn : Parity Error  
Parity에 오류가 발생한 경우

Bit 1 - U2Xn : Double the USART Transmission Speed (Over Clock)  
Internal Clock 등을 사용할 때, Clock 속도가 느릴 때 사용하지만, 주로 쓰지 않는다.

Bit 0 - MPCMn : Multi-Processor Communication Mode  
사용하지 않는다.

- UCSRnB // 몇 번 UART를 사용하냐에 따라 n이 달라진다. AVR은 네 개의 UART 포트가 존재함.

Bit 4 - RXENn : Receiver Enable  
Rx를 Enable한다.

Bit 3 - TXENn : Transmitter Enable  
TX를 Enable한다.

Bit 7 - RXCIEn : RX Complete Interrupt  
얘를 Enable 해두면, 얘에 상응하는 ISR이 존재하므로 데이터가 오면 인터럽트를 수행,  
그 루틴에 가서 데이터를 처리하면 된다.

Bit 6 - TXCIE : TX Complete Interrupt Enable  
거의 사용하지 않는다.

Bit 5 - UDRIEn : USART Data Register Empty Interrupt Enable  
데이터를 갖고 있다면 얘를 빨리 보내줘야 하는데, 인터럽트를 쓰면 오히려 불편할 수 있다. flag를 확인하면서 쭉쭉 보내주면 된다. 보낼 때는 인터럽트를 사용하지 않는 것이 일반적이다.

Bit 2 - UCSZn2 : Character Size   
5bit ~ 9bit 까지 설정 가능.

⇒ 여기서 데이터의 사이즈는 byte가 아니라 bit 수.

Bit 1 - RXB8n : Receive Data Bit 8

Bit 0 - TXB8n : Transmit Data Bit 8  
9번 째 비트를 보내거나 받을 때 그 값을 쓰고 받는 부분.

- UCSRnC

동기, 비동기

패리티 비트

캐릭터 사이즈

Polarity : 앞쪽 엣지로 할 것인지 뒤쪽 엣지로 할 것인지를 정하며, 큰 차이는 없다. 

- UBRRHn, UBRRLn

일반적으로 많이 사용되는 통신 속도를 설정하는 방법은 이미 테이블로 계산되어 있다. 일반적으로 우리는 Clock 속도로 16MHz를 사용한다. 테이블을 보면 Error도 나오는데, 되도록 Error가 적게 발생하는 것이 좋다. Baud Rate를 설정할 때 한 바이트로 설정할 수 없는 경우도 있기 때문에 UBRR Register는 두 바이트를 사용한다.

---

> 인터럽트 사용시 &lt;avr/interrupt.h&gt;를 include 해야 한다.

```c
#define __DELAY_BACKWARD_COMPATIBLE__
#include<avr/io.h>
#define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>
#include <avr/interrupt.h>

// 사전에 계산된 값
// 16MHz의 clock으로 baud rate 9600을 만들기 위해
// 필요한 값 UBRR값 103을 define
#define BAUD_9600 103 
unsigned char send_char = 0;

ISR (INT4_vect) {
	cli();
	_delay_ms(20);
	send_char = 'A';
	EIFR |= 0x10;
	sei();
}

ISR (INT5_vect) {
	cli();
	_delay_ms(20);
	send_char = 'B';
	EIFR |= 0x20;
	sei();
}

int main(void)
{
    cli();
	DDRE &= ~(0x30); // 11'00'1111 스위치 포트를 input으로 설정한다.
	EICRB = 0x0F; // 00 00 11 11 Rising Edge
// 외부 인터럽트 4~7번은 EICRB, 외부 인터럽트 0~3번은 EICRA.

	EIMSK = 0x30; // 00 11 00 00 4, 5번 INT를 사용한다.
// 사용할 외부 인터럽트 마스킹

	EIFR |= 0x30; // 00 11 00 00 4, 5번 flag set to clear
	
	// 우리 보드는 16 bit micro chip을 써서, 16bit int를 사용한다.
	// 혹시 두 바이트를 넘어가는 것이 있다면, 상위 바이트의 값을 넣기 위해
	// H에서는 >> 8을 해서 넣어준다. (256이상의 경우)
	UBRR1H = (unsigned char) (BAUD_9600 >> 8);;
	UBRR1L = (unsigned char) (BAUD_9600);
// 보드 레이트 설정.
	
	// Receiver Enable, Transmitter Enable
	UCSR1B = (1 << TXEN1) | (1 << RXEN1);

// 데이터 사이즈 8로 설정.
	UCSR1C = (1 << UCSZ11) | (1 << UCSZ10);
	sei();

// 다른 부가적인 설정들 parity bit, stop bit는 default가 0이라서
// 따로 추가적인 설정을 해 줄 필요가 없다.
	
    while (1) 
    {

// 인터럽트가 발생하면 send_char의 값이 설정된다.
		if (send_char) {
	
			// UCSR1A에서 UDRE1의 값을 check하는데,
			// 만약 이 값이 0인 상태라면 현재 비어있지 않은 상황.
			// 즉, 다른 작업이 수행 중인 상황이므로 대기해야 한다.
			// 값이 1이 되면 UDR이 빈 상태가 되어 값을 새롭게 쓸 수 있으므로
			// while을 탈출한다.
			while (!(UCSR1A & 1 << UDRE1));
			
			// 값을 쓰고, send_char를 초기화한다.
			UDR1 = send_char;
			send_char = 0;
		}
    }
	
	return 0;
}
```
