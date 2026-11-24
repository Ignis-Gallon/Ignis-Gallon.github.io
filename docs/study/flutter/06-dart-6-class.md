---
title: "Dart 6: Class"
date: 2023-02-18
last_modified_at: 2023-02-19
category: study
tags: [Dart, 클래스, 생성자, 상속, mixin]
---

# Dart 6: Class

Flutter에서는 모든 것이 Class이므로, Class가 매우 중요하다. function 내에서 variable을 사용할 때는 var를 사용하면 된다. 타입을 명시할 필요가 없다. 하지만, Class 내에서 property를 정의할 때는 타입을 정확히 명시해주어야 한다. 

## class의 선언 및 객체 생성

```dart
class Player {
  String name = 'nico';
  int xp = 1500;
}

void main() {
  var player = Player();
}
```

class의 instance를 생성할 때, new를 붙여도 되지만 필수적인 것은 아니다.

만약, property의 값을 바꾸지 못하게 만들려면 final을 추가하면 된다.

## 메서드

```dart
class Player {
  String name = 'nico';
  int xp = 1500;

  void sayHello() {
    print("Hi my name is $name");
  }
}
```

보통 class 내에서는 property를 참조할 때 this를 많이 사용하지만, dart에서는 this를 사용하지 않아도 된다. this.name도 작동은 하지만, 필수적이지 않으며 class method 내에서 this를 사용하지 않는 것을 추천한다. 만약 method 내에 같은 이름의 변수가 있다면 this를 사용해도 되지만, 사용하지 않는 것이 추천된다.

## 생성자

```dart
class Player2 {
  late final String name;
  late int xp;

  Player2(String name, int xp) {
    this.name = name;
    this.xp = xp;
  }
}
```

생성자는 class의 이름과 똑같이 선언할 수 있으며, 값을 늦게 대입해주기 때문에 late 키워드를 붙여주어야 한다. 이 방법이 조금 번거롭다면, 아래와 같이 축약해서 사용할 수 있다.

```dart
class Player2 {
  final String name;
  int xp;

  Player2(this.name, this.xp);
}
```

생성자에서 this를 이용해서 property를 바로 지정해주고, late 키워드를 빼면 된다. 단, 위치에 주의해서 선언해야 한다.

## Named Constructor

```dart
class Player3 {
  final String name;
  int xp;
  String team;
  int age;

  Player3(this.name, this.xp, this.team, this.age);

  void sayHello() {
    print("Hi my name is $name");
  }
}
```

class의 property가 많아질 경우, Named Arguments를 사용할 수 있다. (너무 많은 positional argument가 서로 이웃해 있으면 혼란스러워진다.) 이 때도 마찬가지로, 생성자의 parameter들을 드래그한 뒤에, { 를 입력하면 처리된다.

```dart
class Player3 {
  final String name;
  int xp;
  String team;
  int age;

  Player3({this.name, this.xp, this.team, this.age});

  void sayHello() {
    print("Hi my name is $name");
  }
}
```

```dart
class Player3 {
  final String name;
  int xp;
  String team;
  int age;

  Player3({this.name, this.xp, this.team, this.age});

  void sayHello() {
    print("Hi my name is $name");
  }
}

void main() {
	var player3 = Player3 (
	    name: "nico",
	    xp: 1200,
	    team: 'blue',
	    age: 21
	  );
}
```

하지만 코드를 이렇게만 작성하면, 이전과 마찬가지로 인자가 null이 될 수 있다는 에러 메시지가 뜨게 된다. 이 경우, 인자 앞에 required를 붙여서 해결해줄 수 있다.

```dart
Player3({
    required this.name, 
    required this.xp, 
    required this.team, 
    required this.age
  });
```

## Recap

우리가 API로부터 여러 Player가 들어있는 목록을 전달받는다고 가정해보자. 

```dart
var apiData = [
    {
      "name": "nico", 
      "team": "red", 
      "xp": 0
    },
        {
      "name": "lynn", 
      "team": "red", 
      "xp": 0
    },
        {
      "name": "dal", 
      "team": "red", 
      "xp": 0
    }
  ];
```

이와 같은 데이터가 있다고 할 때, 우리는 이 데이터를 전달받아 class로 만들 수 있다. 대부분의 flutter 앱은 fromJson이라는 named constructor를 만드는 방식으로 API로부터 데이터를 전달받는다.

```dart
class Player {
  final String name;
  int xp;
  String team;

  Player.fromJson(Map<String, dynamic> playerJson)
      : name = playerJson['name'],
        xp = playerJson['xp'],
        team = playerJson['team'];

  void sayHello() {
    print("Hello my name is $name");
  }
}
```

이와 같이 class를 생성하고, main에서 forEach를 이용하여 각각에 대하여 sayHello()를 동작시킬 수 있다.

```dart
void main() {
  var apiData = [
    {"name": "nico", "team": "red", "xp": 0},
    {"name": "lynn", "team": "red", "xp": 0},
    {"name": "dal", "team": "red", "xp": 0}
  ];

  apiData.forEach((playerJson) {
    var player = Player.fromJson(playerJson);
    player.sayHello();
  });
}
```

## Cascade Notation

```dart
class Player {
  String name;
  int xp;
  String team;

  Player({required this.name, required this.xp, required this.team});

  void sayHello() {
    print("Hi my name is $name");
  }
}

void main() {
  var nico = Player(name: 'nico', xp: 1200, team: 'red');
  nico.name = 'las';
  nico.xp = 1200000;
  nico.team = 'blue';
}
```

위와 같은 코드가 있다고 할 때, 값을 변경하는 방법을 nico. 으로 이어서 쓰는 것은 다소 불편한 방식이라고 볼 수 있으므로 Cascade operator를 사용할 수 있다.

```dart
void main() {
  var nico = Player(name: 'nico', xp: 1200, team: 'red')
    ..name = 'las'
    ..xp = 1200000
    ..team = 'blue';
}
```

이는 바로 위의 코드와 똑같은 기능을 한다. class 생성 직후가 아니어도, 나중에도 사용 가능하다.

```dart
var potato = nico
    ..name = 'nico'
    ..xp = 120
    ..team = 'blue'
    ..sayHello();
```

이와 같이 나중에 새로운 변수를 만들 때, 그 변수에 넣을 데이터인 nico를 먼저 Cascade Notation으로 변경한 후에 값을 대입할 수 있으며, 이 경우 potato와 nico는 같은 값을 가지게 된다.

## Enums

flutter에는 색상, margin, padding, 스타일, 정렬 등 정말 많은 옵션들이 있는데 그래서 오타가 날 수도 있다. 개발자들이 이런 실수를 하지 않도록 만들 수 있는 것이 Enums이다.

```dart
enum Team { red, blue }
```

enum은 이와 같이 선언할 수 있으며, 문자열로 선언해도 되고, 아니어도 된다.

```dart
class Player {
  String name;
  int xp;
	Team team;

  Player({required this.name, required this.xp, required this.team});

  void sayHello() {
    print("Hi my name is $name");
    print("$name, $xp, $team");
  }
}
```

이후에는 Player class 내의 property인 team의 타입을 Team으로 바꾸어주면 된다. Enum은 새로 만들기보단 앞으로 자주 사용하게 될 것이다.

## Abstract class

추상화 클래스는 다른 클래스들이 직접 구현해야하는 메서드들을 모아 놓은 일종의 청사진이다.

```dart
abstract class Human {
  void walk();
}

class Player extends Human {
  String name;
  XPLevel xp;
  Team team;

  Player({required this.name, required this.xp, required this.team});

  void sayHello() {
    print("Hi my name is $name");
    print("$name, $xp, $team");
  }

  @override
  void walk() {
    print("I'm walking...");
  }
}
```

위와 같이 abstract class Human을 만들고, Player가 Human을 상속받도록 하였다. 이 경우, Player는 반드시 walk 메서드를 구현해야 하며, 이 과정에서 선택적으로 annotation을 달아 override를 표현해 줄 수 있다.

## Inheritance

```dart
class Human {
  final String name;
  Human(this.name);
  void sayHello() {
    print("Hi, my name is $name");
  }
}

enum Team { blue, red }

class Player extends Human {
  final Team team;
  
  Player({
    required this.team,
    required String name
  }) : super(name);
}

void main() {

  var player = Player(team: Team.red, name:'nico');
}
```

super라는 키워드를 이용하면 상속받은 부모 클래스와 상호작용 할 수 있게 된다. 만약 코드를 아래와 같이 수정하게 되면 super에서도 named argument를 사용할 수 있게 된다.

```dart
class Human {
  final String name;
  Human({required this.name});
  void sayHello() {
    print("Hi, my name is $name");
  }
}

enum Team { blue, red }

class Player extends Human {
  final Team team;
  
  Player({
    required this.team,
    required String name
  }) : super(name: name);
}

void main() {

  var player = Player(team: Team.red, name:'nico');
}
```

## Mixin

Mixin은 생성자가 없는 클래스를 의미한다. 이는 클래스에 프로퍼티들을 추가할 때 사용하며, 매우 유용하다.

```dart
class Strong {
  final double strengthLevel = 1500.99;
}

class QuickRunner {
  void runQuick() {
    print("ruuuuuun!");
  }
```

이와 같은 class가 있다고 할 때, Player class가 두 class의 property와 method를 갖게 하고 싶다면, with 키워드를 사용하면 된다.

```dart
class Player extends Human with Strong, QuickRunner {
  final Team team;

  Player({
    required this.team,
    required String name,
  }) : super(name: name);

  @override
  void sayHello() {
    super.sayHello();
  }
}
```

with을 사용하면 다른 클래스의 property와 method를 긁어와서 사용할 수 있다고 보면 된다. Mixin은 여러 클래스에 여러번 사용할 수 있다. 중요한 것은 Mixin은 생성자가 없는 클래스여야 한다.
