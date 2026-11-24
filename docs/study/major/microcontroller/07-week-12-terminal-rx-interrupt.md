---
title: "12주차: 터미널 통신, 수신 인터럽트"
date: 2022-11-21
last_modified_at: 2022-11-22
category: major
tags: [마이크로컨트롤러응용, AVR, UART, 수신 인터럽트]
---

# 12주차: 터미널 통신, 수신 인터럽트

작성일 2022-11-21 · 최종 수정일 2022-11-22
{ .post-date }

- CatTerm

실제로 전송되는 데이터는 변하지 않고, 보여주는 형식을 ASCII로할지 HEX로 할지만 달라진다.

RET를 누르면, carriage return (0D)가 전달된다. 이게 없으면 한 줄로 쭉 간다는 것.

&lt;TX&gt; Line Feed : 0A를 넣어주면 carriage return이랑 비슷함. (유닉스 스타일로 보인다.)

줄을 바꾼다는 것은 무슨 의미인가? 사실 우리가 데이터를 입력할 때는, 보통 Enter를 눌러서 carriage return을 주고 나서야 데이터가 전달된다. 여기서도 마찬가지로, 데이터를 보내면 받는 쪽에서 이를 받아야 한다. &lt;TX&gt;에 쓰고싶은 말을 써보면 계속 붙어서 데이터가 갈 것이다. 그렇다면 어디서 끝이 날까? 모른다. UART는 기본적으로 한 바이트씩 데이터를 전달받는다. 그런데, 어디부터 어디까지 받아야 하는지를 모른다. 그렇기 때문에 보내는 쪽과 받는 쪽이 특정한 약속이 되어있어야 한다. 특히 ASCII로 보내면, carriage return을 꼭 붙여줘야 한다. 라인 피드는 없어도 되지만, 붙인다면 \\n 앞에 붙여야 한다.

scanf와 같은 것은, block device라고 한다. 얘네는 carriage return이 와야 전달을 끊고 데이터를 읽어온다. 이것을 low level로 구현해야 한다.

마찬가지로, 우리가 보낼 때도 bulk로 보낼 수 있다. 

---

strncpy를 이용하면, arguments로 length를 넣어주어야 한다. 이걸 쓰는 것이 좋다.

- 특정 변수의 값을 확인할 필요가 있을때
- 특정 레지스터 값을 확인할 필요가 있을때
- 우린 Formatted 출력 방식이 절실히 필요하다.

---

폴링에 의한 문제를 해결하고자, 인터럽트 루틴으로 한다.

- 두 부분만 추가

UART 초기화에서 UCSR1B 레지스터의 RXCIE1을 enable해준다. 이전에는 UCSR1A & (1 &lt;&lt; RXC1)을 계속해서 체크했는데, 이제는 그럴 필요 없이 바로 ISR에서 UDR1을 읽어주기만 하면 된다.

ISR(USART1_RX_vect) 을 만들어 주면, 굳이 main에서 while을 구성할 필요없이 ISR에서만 loop-back을 처리할 수 있다. 앞으로는 받을 때는 계속 인터럽트로만 받아줄 것이다.

---

가변적인 입력을 받아주려면, “LED_ON_OFF”, “LED_ALT”, “LED_SHIFT”등과 같은 동작을 할 수 있을 것이다. (단, 미리 약속되어 있어야함.) 이렇게 되면 한바이트씩 날라온다. 예를 들어 “LED_ON_OFF”가 날라온다고 하면 L, E, D, \_, … 이런 식으로 전달되는 것이다. 그런데 우리는 string으로 확인해야 하기 때문에, 받는 스트링을 버퍼링해야 한다. 그걸 우리는 수신 버퍼링해야 한다. buffer에다가 저장을 해야 비교할 수 있음. 그리고 한 문장이 끝나는 것을 확인하기 위해 carriage return (termination character)를 넣어줘야 한다. 실제 문장과는 관계없는 Line Feed(LF), Carriage Return(CR)과 같은 것이다. 

ISR에서는 버퍼링을 하다가 carriage return이 들어오게 되면 main(application)에 event를 보내줘서 string을 parsing한다. parsing한 그 값이 무엇과 같은지 비교해서 해당하는 액션을 취하면 된다.
