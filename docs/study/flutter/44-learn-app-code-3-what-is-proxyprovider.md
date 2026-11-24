---
title: "Learn App Code 3. What is ProxyProvider?"
date: 2023-07-11
last_modified_at: 2023-07-11
category: study
tags: [Flutter, Provider, ProxyProvider]
---

# Learn App Code 3. What is ProxyProvider?

- ProxyProviders

프록시 프로바이더는 두 개의 종류로 나뉘어진다.

1. ProxyProvider0
2. Others

---

- Documentation을 보면, 아래와 같이 경고하고 있다.

Don’t create your object from variables that can change over the time.

```dart
int count;

Provider(
	create: (_) => MyModel(count);
	child: ...
)
```

If you want to pass variables that can change over time to your object, consider using ProxyProvider

```dart
int count;

ProxyProvider0(
	update: (_, __) => MyModel(count),
	child: ...
)
```

Provider는 그 자체로 스스로 업데이트가 되지 못하지만, ProxyProvider는 그 자체로 업데이트를 할 수 있다. 이것이 ProxyProvider가 child widget을 업데이트 하기 위해 초기에 update parameter를 갖는 이유이다.

⇒ 즉, Provider 내부에 위치하지 않은 외부의 데이터가 바뀌는 것은 Provider 만으로는 파악할 수 없으므로, ProxyProvider0를 이용해서 외부의 데이터가 바뀔 때마다 이를 구독하고 있는 대상이 update될 수 있도록 하는 것이다.

**Proxy Provider will update its child widget whenever the data that is passing, changes.**

---

- ProxyProviders 간의 차이는 무엇일까?

    - ProxyProvider0
    - Others

- ProxyProvider&lt;T, R&gt;

T : Data object type that is being listened  
⇒ 구독하고 있는 데이터의 타입.

R : Result object type to be passed down in its update params  
⇒ 결과적으로 보내게 될 데이터의 타입.

- ProxyProvider0&lt;R&gt;

ProxyProvider0는 R만 가지고 있음. ProxyProvider0는 **다른 Provider로부터 데이터를 공급할 수 있는 존재**이다. 하지만 이전 예제에서는 다른 Provider로부터 데이터를 가져온 것이 아니라, 그냥 property로 선언한 int counter의 값을 사용했었는데, 이것은 권장되는 사용 방식은 아니다.

---

- Accurate way to use proxy providers are with a multi provider
