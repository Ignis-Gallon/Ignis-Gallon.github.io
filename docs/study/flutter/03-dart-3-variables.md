---
title: "Dart 3: Variables"
date: 2023-02-15
last_modified_at: 2023-02-16
category: study
tags: [Dart, 변수, null safety, final, const]
---

# Dart 3: Variables

작성일 2023-02-15 · 최종 수정일 2023-02-16
{ .post-date }

## main

- main 함수는 모든 Dart 프로그램의 Entry Point이기 때문에 아주 중요하다.

프로그램에 main이 없으면 실행되지 않는다. dart는 자동적으로 main함수를 찾고, dartpad에도 마찬가지이고 vsCode 에서도 마찬가지이다. 

dart에서는 일부러 세미콜론을 쓰지 않을 때가 있는데, 이 때문에 세미콜론을 잘 구분해서 작성해야 한다. (javaScript나 typeScript에서는 formatter가 자동으로 달아주기도 함.)

## var

- var를 통해 변수를 선언할 수 있다. (자동 타입 생성)

dart에는 타입이 있어서 변수를 업데이트 할 때는 변수의 타입을 잘 지켜야 한다.

- 혹은 명시적으로 타입을 지정할 수 있다.

String name = ‘name’;

관습적으로 함수나 메소드 내부에 지역 변수를 선언할 때에는 var를 사용한다.

그리고 class에서 변수나 property를 선언할 때에는 타입을 지정해준다.

## dynamic

- dynamic은 여러가지 타입을 가질 수 있는 변수에 사용하는 키워드이다.

사용이 추천되지는 않지만, 가끔은 매우 유용하게 사용할 수 있다.

dynamic이 왜 필요한가? ⇒ 변수가 어떤 타입인지 알기 어려울 때가 있을 것이다. 특히 json과 flutter를 이용하여 작업하다보면 그런데, 가끔씩 dynamic으로 살짝 돌아가는 게 유용한 경우도 있기 때문에 사용된다. var를 써도 되지만, dynamic을 써도 된다.

만약 인터넷에서 데이터를 받았는데 그 데이터의 타입을 모른다면 dynamic을 통해 확인하면 매우 도움이 된다. 단, 이상적으로는 dynamic의 사용을 피해야한다.

## null safety

기존에는 없던 기능이지만, 매우 좋은 기능이다. null safety는 개발자가 null 값을 참조할 수 없도록 하는 것이다. 이 기능이 다이다. 만약 코드에서 null을 참조하게 되면, 런타임 에러가 발생한다. 이상적으로는 컴파일 전에 이 에러를 잡아내는 것이 좋다.

dart에서는 어떤 변수가 null이 될 수 있음을 정확히 표시해야 한다. (nullable) 

기본적으로 모든 변수는 non-nullable이다.

변수에 ?를 찍어서 값이 존재하는지 아닌지 확인하고 연산을 진행할 수도 있다.

ex) 

```dart
String? nico = 'nico';
nico = null;
nico?.isNotEmpty;
```

## Final

한 번 정의된 변수를 수정할 수 없게 만들려면, final로 바꿔줘야 한다.

```dart
final name = 'nico'; // constant variable
```

## Late

late는 초기 데이터 없이 변수를 선언할 수 있게 해준다. 변수를 만드는데 데이터가 없는 것이다. 그리고 나서 뒤에서, 무엇인가를 하는 것이다. 예를 들면, API 요청을 받은 다음 데이터를 넣고나서 무언가를 해줄 수 있다.

```dart
void main() {
	late final String name;
	name = 'nico';

	// 여기 불가능.
	// name = '12';
}
```

대부분의 경우, 변수가 가질 데이터의 타입을 알기 때문에 타입을 명확히 지정해주게 될 것이다. 하지만, 그렇지 않은 경우가 있다. API에서 얻어온 값을 사용할 때도 있는데, 이럴 때는 late final을 사용하면 된다.

## Const

JS나 TS의 const는 dart의 final과 비슷하고, dart에서의 const는 다른 기능을 한다. dart에서 const는 compile-time constant를 만들어 준다. 이건 아주 중요한 것으로서, 다음 예제를 통해 확인 가능하다.

```dart
void main() {
	const name = 'nico';
	name = '';
}
```

const의 중요한 점은, compile-time에 알고 있는 값이어야 한다는 것이다. 예를 들어 API 키가 있다고 하고, 그걸 const 안에 넣어두면 절대 바뀌지 않을 것이며, 컴파일이 될 때 그 값을 알고 있을 것이다. 즉, 하드 코딩 된 것이다. 하지만 만약에 API 요청을 한다고 해보자. (사용자의 휴대폰에서 앱을 실행할 때 이루어지는 것들, 예를 들면 fetchAPI) 이것은 compile-time constant가 아니다. 컴파일러는 API 변수의 값을 모른다. 즉, 이것은 const가 아닌 final이 되어야 한다. 

> const는 컴파일 이전에 값을 알아야만 하는 것이고, final은 어떤 값인지 모르고, 그 값이 API 등이나 사용자가 화면에서 입력하는 값이라면 final 혹은 var가 될 것이다. (final은 값이 한 번 지정되면 변하지 않음.) ⇒ late const는 불가능. const는 하드 코딩 되어야 하는 값이기 때문이다.
