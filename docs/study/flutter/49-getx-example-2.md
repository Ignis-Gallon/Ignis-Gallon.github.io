---
title: "GetX Example 2"
date: 2023-03-12
last_modified_at: 2023-03-12
category: study
tags: [Flutter, 상태 관리, GetX, GridView, http]
---

# GetX Example 2

- GetX를 사용하여 Grid에 외부 api에서 가져온 이미지, 텍스트 등을 출력하는 App
- GridView, Json, HTTP 통신에 대한 기본 지식 필요.

---

1. GridView.builder 생성. 원하는 방식으로 총 20개의 Grid를 만들어준다.  
    ⇒ flutter_staggered_grid_view 라는 패키지가 있는데, 강의 촬영 당시 이슈가 있어 사용 X
2. 기본 틀이 갖춰졌으므로, 화면에 보여줄 데이터를 담을 Model을 만들어준다. API로 부터 데이터를 전달받을 것이고 이 API를 우리의 모델에 통합시켜 주어야 한다.
3. JSON을 복사해서 넣어주면, Model을 만들어주는 사이트가 있다.

    - JSON : [http://makeup-api.herokuapp.com/api/v1/products.json?brand=maybelline](http://makeup-api.herokuapp.com/api/v1/products.json?brand=maybelline)
    - Model 자동 생성 : [https://app.quicktype.io/](https://app.quicktype.io/)

    ⇒ 이전에는 null safety가 적용되어 있지 않아서 모델 생성이 불가능했는데, 이제는 가능하다. 여기서는 강의에서 주어진 코드를 사용한다.

4. 이번 예제에서는 controller를 하나만 사용하기 때문에 controller의 이름을 controller로 지정한다. 이후, Controller를 생성한다.
5. my_page에서 controller를 D.I 시키고, GridView를 Obx로 감싼다. 이후, itemCount를 controller.productList.length로 바인딩 시켜준다.
6. http 패키지 설치
7. 기능의 분산을 위하여 data 라는 directory를 만들고, services.dart에서 http 통신을 담당하는 클래스를 만들어준다. 이 과정에서, 서버가 열리고 닫힐 때마다 새롭게 HTTP Client로부터 연결을 시도하는 것은 다소 비효율적이기 때문에 client 객체를 static으로 만들고,  fetchProducts() 메서드도 static으로 만들어서 사용한다.
8. 전달받은 jsonData를 product_model에 존재하는 productFromJson을 이용하여 변환하고, return한다.
9. controller에서 static으로 만들었던 Services class를 import하고, fetchProducts를 통해 데이터를 전달받는다.
10. 디자인을 만들기 위해 view 폴더에 product_tile.dart를 생성한다. (코드는 가져옴) 여기서 이미지 URL을 설정해주고, 좋아요 기능 구현을 위해 GetX를 사용한다.

11. product_model에서 like라는 bool 타입 변수를 만들고, 이를 obs로 만들어준다. 이렇게 되면, like는 Rxbool 타입의 변수가 된다. 이를 product_tile.dart에서 Obx로 감싼 CircleAvatar의 child에서 사용하게 되는데, 삼항 연산자를 이용하여 product.like.value에 따라서 Icon이 나오는 것을 다르게 해주고, onPressed에서는 product.like.toggle() 메서드를 이용하여 전환을 해주도록 한다.

    현재 like는 Observable이고, 그 값을 product_tile에서 icon을 구분하는데 사용하고 있다. 여기에 onPressed로 product.like.toggle()을 사용하게 되는데, toggle() 메서드가 실행되면 그 값이 변경될 것이고, Observable은 자신의 값이 변하게 되므로 자신을 listen하고 있던 Obx를 통해 값을 바꿔주게 된다.

12. MyPage로 가서, ProductTile을 itemBuilder에서 return하도록 만들어준다.

<!-- -->

- 도장 깨기
    1. 어떻게 로딩 인디케이터를 구현할 것인가? (로딩 인디케이터를 위한 새로운 변수를 만들고, 이 역시 obs로 만들면 된다.)
    2. Add to Cart 버튼을 누를 때마다 Cart에 담기는 아이템의 개수가 증가하도록 만든다. (Controller를 만들면 될 것)
