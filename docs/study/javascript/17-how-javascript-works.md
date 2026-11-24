---
title: "자바스크립트 동작원리"
date: 2023-07-23
last_modified_at: 2023-07-23
category: study
tags: [JavaScript, 이벤트 루프, 비동기]
---

# 자바스크립트 동작원리

[개발자 90%가 모르는 자바스크립트 동작원리 (Stack, Queue, event loop)](https://www.youtube.com/watch?v=v67LloZ1ieI)

웹 브라우저 내부에는 Stack이 하나 존재하고, Heap에 존재하는 변수들을 가져와서 사용하게 된다. 자바스크립트는 single threaded language이다.

만약 setTimeout과 같은 함수가 실행되면, 이것은 바로 실행할 수 없는 코드이기 때문에 잠시 Event Queue에 보관시키게 되며, 남은 코드를 먼저 실행하고 이후 시간에 맞춰 다시 실행해준다.

- 아래와 같은 요청들은 Event Queue에 대기시킨다.

    - API
    - EventListener
    - setTimeout

Event Queue에 있던 요청들은 순서에 맞춰 Stack에 다시 올려지게 되는데, 중요한 것은 **Stack이 텅 비어있을 때만 올려진다**는 것이다.

---

자바스크립트에서 복잡한 연산, 오래 걸리는 연산을 시키면 안된다. 그러면 이러한 작업들이 Stack을 비우지 못하게 되고, 그렇기 때문에 시간이 되어도 API, EventListener, setTimeout에 대한 처리가 이루어지지 않을 수도 있다.

따라서 Stack을 바쁘게 만들면 안된다.

그리고 Queue도 바쁘게 하면 안된다. 

---

자바스크립트는 원래 동기적으로 처리가 되는 언어이다. 그런데 가끔씩은 비동기적인 처리를 할 수가 있게 된다. (setTimeout, EventListener, Ajax 함수 등)
