---
title: "Kotlin"
---

# Kotlin

2022년 9월에 Kotlin으로 안드로이드 앱 개발을 공부하면서 정리한 노트입니다. 개념은 서적 "깡쌤의 안드로이드 프로그래밍 with 코틀린"을 보고 정리했고, 활용은 Jetpack, 뷰 바인딩, 옵저버 패턴, RxJava를 실습하며 적었습니다.

**개념**

- [안드로이드 앱 개발의 특징](01-android-app-characteristics.md): 컴포넌트를 기반으로 하는 안드로이드 앱의 구조와 컴포넌트의 종류
- [Class](02-class.md): 주 생성자와 보조 생성자, 상속, 클래스의 종류
- [람다 함수와 고차 함수](03-lambda-and-higher-order-functions.md): 람다 함수의 선언 규칙과 it 키워드, 반환값

**활용**

- [JetPack > viewModel & LiveData](04-viewmodel-livedata.md): viewModel의 생명주기와 LiveData, 숫자를 더하고 빼는 예제
- [viewBinding](05-viewbinding.md): findViewById를 대신해 뷰에 안전하게 접근하는 ViewBinding
- [dataBinding](06-databinding.md): XML 레이아웃에서 뷰와 데이터를 연결하는 DataBinding
- [Observer Pattern from Scratch](07-observer-pattern-from-scratch.md): 옵저버 패턴을 인터페이스부터 직접 구현
- [RxJava 예제 > Observable Example Code](08-rxjava-observable-example.md): Observable을 만들고 구독하는 실습 코드
- [RxJava 예제 > Subject Example Code](09-rxjava-subject-example.md): Subject 네 가지를 실습한 코드
