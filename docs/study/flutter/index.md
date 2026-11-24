---
title: "Flutter"
---

# Flutter

2023년 2월부터 7월 사이에 Flutter를 공부하며 정리한 노트입니다. Flutter와 React Native를 비교한 영상, Dart 문법, 노마드코더의 웹툰 앱 강의, Coding Chef 강의, provider 영상을 보며 적은 기록과, 약 복용 관리 앱을 만들면서 공부한 상태 관리(Provider, GetX)와 개발 팁을 묶었습니다.

앱을 만든 과정은 개발 탭의 [약은 먹고 다니냐?(약 복용 관리 앱)](../../dev/medicine-app/index.md) 개발 일지에 있습니다.

**Flutter vs React Native**

- [Flutter vs React Native](01-flutter-vs-react-native.md): 사용 언어와 개발자 풀, 기본 제공 기능과 서드파티 의존성, 언제 무엇을 고를지

**Dart**

- [Dart 1: Why Dart?](02-dart-1-why-dart.md): 웹용과 네이티브용 두 컴파일러, 두 가지 컴파일 방식, null safety
- [Dart 3: Variables](03-dart-3-variables.md): main 함수, var와 명시적 타입, dynamic, null safety, final, late, const
- [Dart 4: Data types](04-dart-4-data-types.md): 기본 자료형과 num, List, Map, Set, 문자열 보간, collection if와 collection for
- [Dart 5: Functions](05-dart-5-functions.md): arrow function, named parameter와 required, 선택적 위치 매개변수, ??와 ??= 연산자, typedef
- [Dart 6: Class](06-dart-6-class.md): 생성자와 named constructor, cascade notation, enum, 추상 클래스, 상속, mixin

**노마드코더 웹툰 앱**

- [1.3 How Flutter Works](07-nomad-1-3-how-flutter-works.md): 운영체제의 위젯을 쓰지 않고 엔진이 직접 그리는 구조, 프레임워크·엔진·Embedder의 역할
- [2.0 Installation](08-nomad-2-0-installation.md): flutter doctor로 확인한 설치 문제와 해결 과정
- [2.3 Hello World](09-nomad-2-3-hello-world.md): runApp, StatelessWidget의 build, MaterialApp과 CupertinoApp, Scaffold와 appBar
- [3.1 Developer Tools](10-nomad-3-1-developer-tools.md): Column과 Row, SizedBox, 색상 지정, Developer Tools로 위젯 트리 살피기
- [3.2 Buttons Section](11-nomad-3-2-buttons-section.md): MainAxisAlignment와 CrossAxisAlignment, SizedBox, Container, Padding
- [3.3 VSCode Settings](12-nomad-3-3-vscode-settings.md): const를 권하는 이유, 저장할 때 const를 자동으로 붙이는 설정
- [3.5 Reusable Widgets](13-nomad-3-5-reusable-widgets.md): Extract Widget, spaceBetween, final과 required, 위젯 테스트 파일의 오류, Key의 역할
- [3.7 Icons and Transforms](14-nomad-3-7-icons-and-transforms.md): Transform.scale과 Transform.translate, clipBehavior
- [3.8 Reusable Cards](15-nomad-3-8-reusable-cards.md): 카드 위젯을 클래스로 분리하기, 삼항 연산자로 색 고르기, SingleChildScrollView
- [3.9 Code Challenge](16-nomad-3-9-code-challenge.md): 3장 코드 챌린지의 결과 코드(main.dart, currency_cards.dart)
- [4.0 State](17-nomad-4-0-state.md): Stateless Widget과 Stateful Widget의 차이, 위젯과 State 두 부분
- [4.3 BuildContext](18-nomad-4-3-buildcontext.md): MaterialApp의 theme, 위젯 트리, context로 상위 정보에 접근하기
- [4.4 Widget Lifecycle](19-nomad-4-4-widget-lifecycle.md): initState와 dispose의 호출 시점과 쓰임
- [5.0 User Interface](20-nomad-5-0-user-interface.md): Flexible로 영역 비율 나누기, Container의 alignment, Expanded
- [6.1 AppBar](21-nomad-6-01-appbar.md): super.key의 역할, Scaffold와 centerTitle
- [6.2 Data Fetching](22-nomad-6-02-data-fetching.md): http 패키지 설치와 pubspec.yaml, Future와 async, await
- [6.3 fromJson](23-nomad-6-03-fromjson.md): jsonDecode와 dynamic, named constructor로 데이터 클래스 만들기
- [6.4 Recap](24-nomad-6-04-recap.md): Future, 응답 상태 코드 확인, jsonDecode, fromJson 패턴 복습
- [6.5 waitForWebToons](25-nomad-6-05-waitforwebtoons.md): initState에서 API 호출 메서드를 실행해 State에 담기
- [6.6 FutureBuilder](26-nomad-6-06-futurebuilder.md): State 없이 Future 데이터를 그리는 FutureBuilder, builder와 snapshot
- [6.7 ListView](27-nomad-6-07-listview.md): ListView, ListView.builder, ListView.separated의 차이
- [6.8 Webtoon Card](28-nomad-6-08-webtoon-card.md): ListView의 높이 제한 오류와 Expanded, Image.network, Container의 decoration
- [6.9 Detail Screen](29-nomad-6-09-detail-screen.md): GestureDetector, Navigator.push와 MaterialPageRoute, PageRouteBuilder 전환 애니메이션
- [6.10 Hero](30-nomad-6-10-hero.md): 두 화면의 위젯을 같은 tag로 이어 주는 Hero 위젯
- [6.11 Recap](31-nomad-6-11-recap.md): 홈 화면에서 상세 화면까지 만든 내용 복습
- [6.13 Futures](32-nomad-6-13-futures.md): 상세 화면에서 id로 API를 호출하기, StatefulWidget과 initState, late
- [6.14 Detail Info](33-nomad-6-14-detail-info.md): API 주소를 잘못 적어 생긴 오류, FutureBuilder에 대한 의문, Column과 Row의 축
- [6.15 Episodes](34-nomad-6-15-episodes.md): 회차 목록을 Column과 collection for로 보여 주기
- [6.17 Favorites](35-nomad-6-17-favorites.md): shared_preferences 설치와 값 저장·읽기

**Coding Chef**

- [Login_dice](36-coding-chef-login-dice.md): focus tree·focus node·focus scope, Expanded로 이미지 감싸기
- [Null Safety](37-coding-chef-null-safety.md): non-nullable 변수의 규칙, ?? 연산자
- [Weather App](38-coding-chef-weather-app.md): Stateless Widget과 Stateful Widget의 생명 주기, Future와 이벤트 큐
- [Provider](39-coding-chef-provider.md): Provider.of와 context, ChangeNotifier의 한계, extends와 mixin의 차이

**provider**

- [provider 1](40-provider-1.md): ChangeNotifier, Consumer, Provider.of, ChangeNotifierProvider의 역할
- [provider 3](41-provider-3.md): provider로 API 데이터를 불러와 보여 주는 절차
- [Learn App Code 1. What is Flutter Provider?](42-learn-app-code-1-what-is-flutter-provider.md): Provider가 위젯 트리에서 데이터를 전달하는 원리
- [Learn App Code 2. What is MultiProvider?](43-learn-app-code-2-what-is-multiprovider.md): 여러 Provider를 함께 쓰는 MultiProvider, 같은 타입이 겹칠 때의 문제
- [Learn App Code 3. What is ProxyProvider?](44-learn-app-code-3-what-is-proxyprovider.md): 바깥 데이터의 변화를 반영하는 ProxyProvider, 타입 인자

**Navigator**

- [Navigator](45-navigator.md): Route와 스택 구조, push에 context와 MaterialPageRoute를 넘기는 이유

**상태 관리**

약 복용 관리 앱의 [개발 일지 2023.03.11](../../dev/medicine-app/2023-03-11.md)을 쓰면서 함께 공부한 내용입니다.

- [State Management(Provider)](46-state-management-provider.md): 상태 관리가 필요한 이유, setState의 문제점, Provider와 ChangeNotifier
- [State Management(GetX)](47-state-management-getx.md): GetBuilder를 쓰는 단순 상태 관리와 반응형 상태 관리, 의존성 주입, MVC 패턴
- [GetX Example 1](48-getx-example-1.md): 상품 목록과 장바구니 예제, Controller와 Observable, fold로 총액 구하기
- [GetX Example 2](49-getx-example-2.md): 외부 API의 데이터를 GridView에 보여 주는 예제, Model 만들기, http 통신 클래스 분리, Obx

**Flutter 팁**

- [Flutter 팁](50-flutter-tips.md): spaceBetween 등 정렬 방식, 키보드로 생기는 overflow, AlertDialog 안에서 상태 바꾸기, IconButton 패딩, 에뮬레이터 custom skin
