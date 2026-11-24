---
title: "6.1 AppBar"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, AppBar, Scaffold]
---

# 6.1 AppBar

```dart
class App extends StatelessWidget {
  const App({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp();
  }
}
```

여기서 super.key는 어떤 역할을 할까? 이 위젯의 key를 stateless widget이라는 슈퍼 클래스에 보낸 것이다. 이것은 위젯은 ID 같은 식별자 역할을 하는 key가 있다는 것을 의미한다. Flutter가 위젯을 빠르게 찾을 수 있도록 하는 역할을 한다.

- Scaffold : screen을 위한 기본적인 레이아웃과 설정을 제공한다.
- AppBar → centerTitle : true, title을 중앙으로 옮긴다.
- Google fonts에서 font를 불러오는 방법도 있음
