---
title: "Weather App"
date: 2023-03-31
last_modified_at: 2023-07-09
category: study
tags: [Flutter, Coding Chef, 생명 주기, Future]
---

# Weather App

작성일 2023-03-31 · 최종 수정일 2023-07-09
{ .post-date }

- 주요 학습 내용
    1. Widget lifecycle
    2. API
    3. Exception handling
    4. Http package
    5. JSON parsing
    6. Passing data

---

- Stateless Widget

    - Stateless Widget은 한 번 만들어지면, 다시 바뀌지 않는다. 그러므로 Stateless Widget을 변경하고 싶다면 destroy하고 rebuild 해야 한다.
    - 따라서, Stateless Widget의 Life Cycle은 매우 간단하며, build만 신경쓰면 된다.

- Stateful Widget

    - Stateful Widget은 State Object와 결합하게 된다. State Object의 역할은 Widget의 구성요소나 속성을 지속적으로 추적하는 것이기 때문에 내가 원하는 입맛대로 setState를 사용하여 구성요소들을 update할 수 있다.
    - 따라서 Stateful Widget은 Stateless Widget보다 좀 더 긴 Life Cycle을 가진다.

    - Stateful Widget의 대표적인 Life Cycle Method

        - initState
        - build
        - dispose (deactivate)

Life Cycle Method 들을 이용하면, Stateful Widget이 생성됨과 동시에 특정한 동작을 수행하게 할 수도 있고, 혹은 메모리 누수를 막기 위해 Stateful Widget이 파괴되는 순간에 할당된 메모리를 반환하게 할 수도 있다. 

---

- Future

Dart가 Future를 만나면 우선 Event Queue 대기열에 해당 코드를 등록하고 다음 코드를 실행하게 된다. 즉, 병렬적으로 수행하는 경우가 아니라면 Future가 아닌 await을 사용해서 동기적 수행이 되도록 만들어야 한다. (Javascript의 Promise도 마찬가지로 .then으로 이어나갈 경우 병렬적으로 수행이 되지만, await을 사용할 경우 동기적으로 수행된다.)
