---
title: "마이크로컨트롤러응용"
---

# 마이크로컨트롤러응용

2학년 2학기에 수강한 과목입니다. AVR(ATmega2560) 보드로 PORT 입출력, 인터럽트, Neopixel LED, UART 통신, ADC, Timer와 PWM을 실습하며 정리한 주차별 노트와, 학기 말에 주제별로 다시 정리한 노트입니다.

**주차별 노트**

- [4주차: 개발 환경, PORT 제어](01-week-04-dev-env-port.md): PORT를 이용한 기본 입출력, 회로도와 보드 커넥터 번호 읽기, DDR과 PINX, unsigned char를 쓰는 이유
- [6주차: Delay 구현, 임베디드 무한 루프](02-week-06-delay-loop.md): Delay 구현에 필요한 설정, 프로그램이 끝나지 않아야 하는 임베디드 시스템의 무한 루프
- [7주차: 포트 입력, 스위치 연결](03-week-07-port-input-switch.md): 포트 입력과 스위치 연결, 스위치로 LED를 shift하는 코드, Polling의 한계와 인터럽트의 필요성
- [8주차: 인터럽트, ISR](04-week-08-interrupt-isr.md): 인터럽트와 ISR, Trigger 방식, 외부 인터럽트와 PCINT 레지스터, AVR에서 인터럽트를 등록하고 처리하는 절차
- [10주차: Neopixel LED 제어](05-week-10-neopixel.md): WS2812의 핀 구성과 색 데이터 순서, High/Low 시간으로 0과 1을 구분하는 타이밍, 라이브러리로 LED 링 제어하기
- [11주차: UART 통신](06-week-11-uart.md): 동기식과 비동기식 통신, RS232, 송수신 측이 맞춰야 할 항목, UART 레지스터
- [12주차: 터미널 통신, 수신 인터럽트](07-week-12-terminal-rx-interrupt.md): ASCII와 HEX 표시, carriage return과 line feed, RXCIE1을 켜고 ISR에서 수신하기
- [13주차: UART 커맨드 프로토콜, ADC](08-week-13-uart-command-adc.md): STX, command, ETX로 된 프레임과 수신용 FSM, ADC의 해상도와 채널 선택, 가변 저항으로 ADC 값 읽기
- [14주차: Timer, PWM, CTC 모드](09-week-14-timer-pwm-ctc.md): 8bit·16bit 타이머, Overflow와 OCR 인터럽트, 주파수 계산식, CTC Mode와 prescaler, 피에조 부저
- [code](10-code.md): 가변 저항의 ADC 값을 읽어 UART로 출력하는 코드

**주제별 정리**

- [Interrupt](11-interrupt.md): DDR, PORT, PIN의 역할, 스위치 연결 방법, External Interrupt와 Pin Change Interrupt의 레지스터
- [Neopixel](12-neopixel.md): 색 데이터 순서, 프로젝트 사전 설정, mc_ws2812.h의 함수, LED 점멸·순서 출력·위치 지정·Shift·밝기 변화 코드
- [UART](13-uart.md): 송수신 측이 맞춰야 할 설정, UDRn, UCSRnA~C, UBRRn 레지스터의 역할, 수신 인터럽트를 쓰는 방법
- [ADC](14-adc.md): ADMUX로 채널과 기준 전압 고르기, ADCSRA의 비트별 역할, ADCL과 ADCH에서 값 읽기
- [가변 저항, 압력 센서](15-variable-resistor-pressure-sensor.md): 가변 저항과 압력 센서를 ADC에 연결하는 방법
- [Timer(with Buzzer)](16-timer-with-buzzer.md): Timer와 PWM, 관련 레지스터와 동작 모드, 주파수 계산식, 피에조 부저를 울리는 코드
