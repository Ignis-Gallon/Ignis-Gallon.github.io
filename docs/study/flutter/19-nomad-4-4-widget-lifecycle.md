---
title: "4.4 Widget Lifecycle"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, 생명 주기, initState, dispose]
---

# 4.4 Widget Lifecycle

- StatefulWidget은 살아있으며, 생명 주기(Life Cycle)를 갖고 있다. 이 것들은 이벤트에만 반응한다.
- MyLargeTitle 위젯을 StatefulWidget으로 바꿔보자.

StatefulWidget은 build 메서드 이외에 무엇을 가지고 있을까? 이것은 build 이외에도 initState라는 메서드를 가지고 있다.

- initState

InitState는 상태를 초기화 하기 위한 메서드이다. 하지만 이전에 본 것처럼 이 메서드를 꼭 사용하지 않아도 된다. 왜냐면 굳이 이 메서드가 아니어도 초기화는 가능하기 때문이다.

대부분의 경우 이 메서드가 필요하지는 않다. 하지만, 종종 부모 요소에 의존하는 데이터를 초기화해야 하는 경우가 있다. 이 데이터를 초기화하기 위해 context를 사용하게 될 것이다. 종종 API에서 업데이트를 구독하고 싶은 경우가 있을텐데, 이 때 initState를 사용하게 된다. 중요한 점은, initState가 항상 build 메서드보다 먼저 호출되어야 하며, 단 한 번만 호출된다. 그리고 InitState를 호출할 때는 super.initState()를 호출해야 한다.

- dispose : 위젯이 스크린에서 제거될 때 호출되는 메서드이다.

API 업데이트나 이벤트 리스너로부터 구독을 취소하거나 form의 리스너로부터 벗어나고 싶을 때 사용할 수 있다. dispose 메서드는 무언가를 취소하는 것이다. 위젯이 위젯 트리에서 제거되기 전에 무언가를 취소하려고 하려면, dispose를 사용하면 된다.
