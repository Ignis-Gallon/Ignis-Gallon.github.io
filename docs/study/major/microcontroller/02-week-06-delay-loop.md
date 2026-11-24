---
title: "6주차: Delay 구현, 임베디드 무한 루프"
date: 2022-10-11
last_modified_at: 2022-10-11
category: major
tags: [마이크로컨트롤러응용, AVR, Delay, 무한 루프]
---

# 6주차: Delay 구현, 임베디드 무한 루프

- Delay를 구현하기 위해 

```c
#include<util/delay.h> // 이를 추가해줌.
```

라이브러리를 확인해보면, nobe을 계속 실행시키면서 계속 돌아가게 한다.

- 프로그램에서 Delay utility 사용을 위해 다음을 항상 추가 한다.

```c
#include<avr/io.h>

#define FOSC 16000000UL
#define F_CPU 16000000UL
#include <util/delay.h>
```

- 임베디드 시스템에서는 시스템이 끝나면 안된다. 마치 운영체제처럼, 전원을 종료하기 전까지는 계속해서 프로그램이 돌도록 만들어주어야 한다.
- 모든 임베디드 시스템은 전원을 끄기 전까지는 끝나지 않는데, 즉, 무한 루프를 돌면서 사용자로 부터 입력을 받아들이고 이에 대한 액션을 항시 취할 수 있도록 만들어야 한다.
- 코드를 짜보면 안될텐데

```c
#define __DELAY_BACKWARD_COMPATIBLE__
을 넣어서 하위버전과 호환되도록 만들어야 한다.
```

LED 불을 켤 때, 돌아올 때 같은 쪽 불이 다시 켜지지 않도록 만들기.
