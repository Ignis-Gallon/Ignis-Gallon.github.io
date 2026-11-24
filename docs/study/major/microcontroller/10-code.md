---
title: "code"
date: 2022-12-07
last_modified_at: 2022-12-07
category: major
tags: [마이크로컨트롤러응용, AVR, ADC, UART]
---

# code

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
