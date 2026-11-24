---
title: "GetX Example 1"
date: 2023-03-12
last_modified_at: 2023-03-12
category: study
tags: [Flutter, 상태 관리, GetX, Controller]
---

# GetX Example 1

- View Model Controller Pattern 이용, View, Model Controller Directory를 생성한다.
- GetX는 어떤 형태의 데이터이든 삽입할 수 있기 때문에 Stateful Widget을 사용할 필요가 없다.

---

1. Model, View, Controller 구현 (Controller에서 Observable을 만듦)  
    ⇒ 이 과정에서 products를 선언, fetchData 이후 assignAll을 통해 데이터를 가져옴.
2. View에서 Dependency Injection을 통해서 언제든지 원할 때 Controller를 사용하면 Model에서 데이터를 가져올 수 있도록 구현.
3. View에서 ListView는 끊임없이 ShoppingController를 listen하고 있다가 Data가 바뀌면 그 즉시 데이터를 업데이트 해준다.
4. Controller는 어떠한 기능과 관련된 것이다. 그러므로 새로운 기능이 필요할 때는 새로운 컨트롤러를 만들어야 한다. cart_controller.dart 생성.
5. 일단 Controller를 만들고 D.I되면 다시 생성할 필요는 없다. 설령 다른 페이지로 이동해도 Get.find() 메서드로 얼마든지 컨트롤러를 불러올 수 있기 때문이다.
6. cart_controller에서는 item을 보관할 수 있고, 가격의 총합을 구할 것이다. 이를 구하는 방법은 간단하게는 for in loop 정도가 있겠지만, fold를 사용하면 편리하게 구할 수 있다.

```dart
void main() {
	List<int> numbers = [20, 68, 89, 251];
	int sum = numbers.fold(0, (e, p) => e+p);
}
```

1. 당연한 이야기이지만, 이제부터는 그 어떤 요소가 CartController를 listen하던, cartItems가 업데이트 되면 자동으로 동시에 totalPrice 값도 업데이트가 된다. (getter로 가져오기 때문에 필요할 때에 값이 새로 계산돼서 얻어진다.)
2. 각각의 버튼에 대하여 처리해준다.

<!-- -->

- 도장 깨기 : scrollbar()로 listView 감싸기
- floatingActionButton은 Scaffold 내부에 들어가지 않는다.
