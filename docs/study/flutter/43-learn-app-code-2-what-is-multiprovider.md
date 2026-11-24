---
title: "Learn App Code 2. What is MultiProvider?"
date: 2023-07-11
last_modified_at: 2023-07-11
category: study
tags: [Flutter, Provider, MultiProvider]
---

# Learn App Code 2. What is MultiProvider?

[Flutter Multi Provider Simply Explained](https://www.youtube.com/watch?v=GYmrmo5eShg&list=PLgG0-B2KlM1sr3MRmqaV2tVKJf_7Zk7f3&index=2)

- Provider는 여러 개 만들 수 있다.

```dart
Provider<Something>(
	create: (_) => Something(),
	child: Provider<SomethingElse>(
		create: (_) => SomethingElse(),
		child: Provider<AnotherThing>(
			create: (_) => AnotherThing(),
			child: someWidget,
	),
);
```

이와 같이 &lt;**Something<strong>&gt; Provider안에서, child widget을 만들기 전에 &lt;</strong>SomethingElse<strong>&gt;의 Provider로 다시 감싸주면 두 개의 Provider의 흐름이 생기게 될 것이다. (물론, SomethingElse보다 Something이 좀 더 큰 범위를 감싸고 있다.) 그리고, 하나를 더 만들고자 한다면 &lt;</strong>AnotherThing**&gt;에 대한 Provider를 또 다시 만들어줄 수 있다.

하지만, 이런 방식을 사용하면 우리는 **Provider를 사용할 때마다 불필요한 보일러 플레이트 코드들을 많이 작성해야 하고, 직관성도 많이 떨어진다.** 따라서 이럴 때는 MultiProvider를 사용한다.

---

```dart
MultiProvider(
	providers: [
		Provider<Something>(create: (_) => Something()),
		Provider<SomethingElse>(create: (_) => SomethingElse()),
		Provider<AnotherThing>(create: (_) => AnotherThing()),
	],
	child: someWidget,
)

// someWidget widget
@override
Widget build(BuildContext context) {
	final something = Provider.of<Something>(context);
	final something = Provider.of<SomethingElse>(context);
	final something = Provider.of<AnotherThing>(context);
// ...
}
```

⇒ 훨씬 더 직관성이 높아짐. 보일러 플레이트 코드도 줄어듦.

---

- Challenge

```dart
MultiProvider(
	providers: [
		Provider<String>(create: (_) => 'Name: Haris'),
		Provider<String>(create: (_) => 'Age: 22'),
	],
	child: Home(),
)

// Home widget
@override
Widget build(BuildContext context) {

	final text = Provider.of<String>(context);
	print(text);
}
```

Q. 위의 코드에서, print(text);를 하면, 어떤 text가 나오게 될까?

**A.**

정답은 “Age: 22” 이다. 왜냐면, 같은 Type의 Provider이므로, 가장 마지막에 등록한 Provider 데이터가 전달되기 때문이다.

하지만, 이런 것은 분명히 문제가 된다고 볼 수 있다. 이를 어떻게 해결해야 할까?

---

- Solve

가장 간단한 방법은, 데이터를 구별할 수 있도록 별도 Class를 만드는 것이다.

```dart
class Name {
	String text;
}

class Age {
	int number;
}
```

이와 같이 두 개의 데이터를 갖는 model을 만들어서, provider에 전달하고, 실제로 사용할 때는 Class에 접근하여 꺼내 쓴다고 생각하면 된다.

```dart
MultiProvider(
	providers: [
		Provider<Name>(create: (_) => Name('Haris')),
		Provider<Age>(create: (_) => Age(22)),
	],
	child: Home(),
)

// Home widget
@override
Widget build(BuildContext context) {

	final name = Provider.of<Name>(context).text;
	final age = Provider.of<Age>(context).number;
}
```
