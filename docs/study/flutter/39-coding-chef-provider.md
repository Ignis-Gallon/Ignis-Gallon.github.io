---
title: "Provider"
date: 2023-07-09
last_modified_at: 2024-09-19
category: study
tags: [Flutter, Coding Chef, Provider, ChangeNotifier, mixin]
---

# Provider

Provider.of : 공급자(Subject)

context : 수로

⇒ Provider.of&lt;Salmon&gt;(context)

⇒ 여기에 사용되는 .of는, 주어진 context를 거슬러 올라가면서 가장 가까이에 있는 원하는 타입의 instance를 찾아서 반환하라는 의미이다.

Provider 또한 하나의 Widget이므로, Widget Tree 상에서 다른 Widget 들과 똑같은 특성을 갖게 된다.

---

우리가 원하는 widget만 rebuild 시키기 위해서는 ChangeNotifier를 사용해야 함.

- extends와 mixin의 차이점.

관계라는 측면에서의 결속력의 차이가 핵심이다. 상속은 친족의 관계처럼 그 결속이 매우 강력하지만, mixin은 단순히 일부 기능을 추가하고 싶을 때 사용할 수 있게 된다.

마치 interface와 비슷하지만, 그 기능이 이미 정의되어 있어서 override를 할 필요가 없는 것이라고 생각하면 될 듯 하다.

Dart에서 mixin은 개수의 제한이 없다.

이처럼, ChangeNotifier를 Model에 mixin 한다는 것은 ChangeNotifier mixin에 포함된 특정 기능을 현재 class에서 사용한다는 의미이다.

addListener를 이용하여 구독이 가능하지만, 구독 해제 또한 removeListener를 통해 수동으로 해주어야만 한다.

- ChangeNotifier의 한계

    - 매번 수동으로 addListener를 등록해 주어야 함.
    - 수동으로 addListener를 제거시켜 주어야 함.
    - FishModel 인스턴스를 매번 생성자를 통해서 전달 해야 함
    - UI 리빌드도 수동으로 해결해야 함

- ChangeNotifierProvider의 장점

    - 모든 위젯들이 Listen 할 수 있는 ChangeNotifier 인스턴스 생성
    - 자동으로 필요 없는 ChangeNotifier 제거
    - Provider.of를 통해서 위젯들이 쉽게 ChangeNotifier 인스턴스에 접근할 수 있게 해줌
    - 필요시 UI를 리빌드 시켜줄 수 있음.
    - 굳이 리빌드가 필요없는 UI를 위해서 listen : false 옵션을 제공한다.
