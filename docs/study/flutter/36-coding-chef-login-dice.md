---
title: "Login_dice"
date: 2023-03-25
last_modified_at: 2023-03-31
category: study
tags: [Flutter, Coding Chef, Focus, Expanded, Image]
---

# Login_dice

## Focus

- focus tree
- focus node : 포커스를 받는 특정 위젯을 식별할 때 사용된다. 예를 들어 dice를 입력 받는 text field를 찾아서 무언가 focus 관련 이벤트를 지정하고 싶다면 이 focus node라는 것을 사용하여 dice에 text field를 지정해줄 수 있다.
- focus scope : 어떤 위젯들까지 포커스를 받는지 범위를 나타낸다. 

---

## Image with Expanded

```dart
// 이미지 불러오기

// 1
Image(
	image: AssetImage('path'),
	width: 100,
	height: 100,
),

// 2
Image.asset('path'),
```

Expanded Widget으로 Image를 감싸면 Column 위젯이라면 세로로, Row 위젯이라면 가로로 가능한 모든 공간을 차지하게 된다.

Expanded 위젯 내에서 flex라는 속성을 지정해주면, 크기의 비율을 지정하여 Expanded 되도록 만들 수 있다.
