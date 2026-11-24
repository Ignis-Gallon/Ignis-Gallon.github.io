---
title: "4.0 State"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, State, StatefulWidget]
---

# 4.0 State

- Stateless Widget : build 메서드를 통해서 단지 UI를 출력하는 역할을 하는 Widget이다.
- Stateful Widget : State가 존재하여 상태에 따라 데이터가 변하게 되는 Widget이다.

데이터가 변경되어 실시간으로 이 변화를 UI에 반영하고 싶을 때 Stateful Widget을 사용하면 된다.

Stateless는 데이터를 가지고 있지 않다는 뜻이다.

- Stateful Widget은 두 파트로 나뉜다.

    - 상태가 없는 그 위젯 그 자체
    - 위젯의 상태(state) : 위젯에 들어갈 데이터와 UI를 넣는 곳이다.  
        ⇒ 데이터가 변경되면 해당 위젯의 UI도 변경된다.

- ‘st’라고 입력하고 Stateful Widget의 Class를 간편하게 생성하는 방법을 사용하거나 Stateless Widget을 code action으로 Stateful Widget으로 바꿀 수 있다.
- Convert to StatefulWidget : Widget이 두 파트로 나뉘어진다. 

State는 우리가 UI를 구축하는 곳이고, 이 상태는 매우 특별하다. 왜냐면, 우리가 State의 데이터를 바꿀 때 우리 UI는 새로고침되면서 최신 데이터를 보여주기 때문이다.

Stateful Widget의 데이터는 단지, 클래스 프로퍼티일 뿐이다.

- IconButton : onPressed에 클릭할 때마다 실행될 함수, icon 두 가지를 할당해야 한다.
- Stateful Widget은 두 가지 부분이 있다.

하나는 위젯이고, 이 위젯들은 모두 상태를 가지고 있다.

State는 데이터를 가지고 있는 부분이다. 그리고 데이터는 단지 Dart 클래스의 프로퍼티이다.

State 클래스는 위젯의 UI도 가지고 있다. 그래서 State의 데이터가 바뀔 때, 위젯의 UI도 새로운 데이터와 함께 새로고침 된다. (즉, 클래스의 프로퍼티를 바꾸면, 데이터가 업데이트 된다는 것)

하지만, 이 자체로는 업데이트를 할 수 없다.
