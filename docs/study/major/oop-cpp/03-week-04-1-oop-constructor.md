---
title: "4주차-1: OOP 특징, 생성자, Getter/Setter"
date: 2022-09-26
last_modified_at: 2022-10-11
category: major
tags: [객체기반SW설계, C++, OOP, 생성자, Getter/Setter]
---

# 4주차-1: OOP 특징, 생성자, Getter/Setter

## # `OOP의 특징`

- `Encapsulation(캡슐화)` **-&gt; 생산성 증대.**   데이터 처리가 간단해진다.

    데이터와 데이터를 다루는 함수를 같은 곳에 묶어서 코드 재사용성을 극대화 한다.

- `Information hiding(정보은닉)` **→ 생산성 증대.** 추가적인 검증이 줄어든다.

    `public` : 클래스 밖에서도 나의 member variable과 member method 호출이 가능하다.  
    `private` : 클래스 내에서만 member variable과 member method 호출이 가능하다.  
    → Class 밖에서는 호출하거나 참조할 수 없다.  

    접근제어자(Access Controller)를 이용해 클래스 내부에 있는 Variable에 대한 접근 방식을 조절하는 방식. 정보를 숨기기 위해서 데이터를 은닉한다.

- `Inheritance(상속)`  -&gt; **생산성 증대**. 이미 검증된 Parent Class를 상속하면, 오류도 줄어든다.

    `Access Controller`  : 1. public, 2. private, 3. protected  
    public :  Class 외부에서 자유롭게 접근하고 호출할 수 있다.  
    private : Class 외부에서 접근, 호출할 수 없으며 상속되지 않는다.  
    protected : Class 외부에서 보면 private처럼 보이지만, 상속 시에 자식에게는 물려줄 수 있다.

- `Polymorphism(다형성)`

    Overloaded Function(Class 내부에서는 Overloaded Method)을 만들 수 있는 특성을 말한다.  
    하나의 객체가 여러 타입을 가질 수 있다.  

- `Unified Modeling Language(UML, Unified Modeling Language, 통합 설계 언어)`

    객체지향 언어로 큰 시스템을 설계할 때는 UML을 설계하는 사람도 있다.  
    C언어에는 순서도(flow chart)가 있다면, 객체지향에는 UML(그래픽 100%)이 있다.  
    → 다양한 종류가 있으며, 자바, 파이썬, 자바 스크립트 등 OOP로 설계할 때 사용된다.

---

## # `Constructor (생성자)`

- 아주 특수한 함수로서, Class를 선언할 때 선언된 멤버 객체를 초기화하기 위한 특수한 함수이다.  
    Constructor 또한 객체 지향 함수이기 때문에, `Polymorphism`을 적용할 수 있다. (vs 4-2)
- 생성자는 함수이며 반환값이 없다. 객체가 생성될 때, 최초 딱 1회만 실행(호출)된다. 모든 Class는 `constructor`를 갖는다. 이는 `polymorphism`을 지원하며, `default argument`를 지원한다.
- 만약 Constructor를 만들지 않으면, Compile 과정에서 Default Constructor가 생성된다. 만약 Default Constructor를 (빈 상태로) 짤거면 컴파일러가 알아서 해주므로, 짜지 않아도 된다.  
    → 즉, Constructor를 짜지 않는다고 Constructor가 없는게 아니라 자동으로 생성된다.  
    → 그러나, Constructor를 단 한 개라도 만들면, Default Constructor가 생성되지 않는다.
- `Default Constructor`는 ()를 붙이지 않고, 객체로서만 생성해야한다.

```cpp
#include <iostream>

using namespace std;

class Circle {
public:
	int x;
	int y;
	int r;

	Circle(int _x, int _y, int _r) : x(_x), y(_y), r(_r) { // 초기화 간단히 하기
	}

	Circle() : x(10), y(10), r(100) {
		
	}

	void showCircleInfo() {
		cout << x << " " << y << " " << r << endl;
	}
};

int main() {
	Circle c(10, 10, 30);
	Circle cd;
	cd.showCircleInfo();

	return 0;
}
```

- `Destructor`(소멸자)

    객체가 사라질 때 자동으로 불러와지는 특수한 함수.  
    생성자와 마찬가지로 굳이 지정해주지 않으면 `Default Destructor`가 불러진다.  
    그렇다면 소멸자는 왜 만드는 것일까? 예를 들어, Class 내에서 생성 시에 malloc을 했다면, 반드시 사라질 때 malloc을 해제해 주어야 한다. 이를 잊지 않고 해주고자 할 때 소멸자를 이용한다. 선언은 다음과 같이 할 수 있다.

    ```cpp
    #include <iostream>

    using namespace std;

    class Circle {
    public:
    	int x;
    	int y;
    	int r;
    	int* p;

    	Circle(int _x, int _y, int _r) : x(_x), y(_y), r(_r) { // 초기화 간단히 하기
    		p = (int*)malloc(10000);	
    	}

    	Circle() : x(10), y(10), r(100) {
    		p = (int*)malloc(10000);	
    	}

    	~Circle() {
    		cout << "destructor was invoked" << endl;
    		free(p);
    	}

    	void showCircleInfo() {
    		cout << x << " " << y << " " << r << endl;
    	}
    };

    int main() {
    	Circle c(10, 10, 30);
    	Circle cd;
    	cd.showCircleInfo();

    	return 0;
    }
    ```

- Class를 구성할 때 일단 멤버 변수는 기본적으로 `private`으로 짜는 것이 좋다.

    → getter, setter를 구성해주어야 한다.

---

## # `Getter, Setter`

- Getter : 멤버 변수의 값을 읽어오는 함수
- Setter : 멤버 변수의 값을 쓰는 함수
- C++에서는 Getter와 Setter를 모두 만들어줘야 하지만, Swift, Kotlin과 같은 고급 언어의 경우에는 Compiler가 Getter와 Setter를 자동으로 만들어준다.  
    → Getter, Setter는 public으로 선언해준다.

---

## # `함수와 클래스`

- Class는 함수의 argument로 넘어갈 때, 복사본으로 넘어간다. (즉, Call-By-Value)  
    그런데, 복사본이 넘어가면 데이터 사이즈가 너무 클 수도 있고, 포인터를 넘기기에는 위험하다.  
    이에 따라 Reference(참조자)를 사용해서, 주소를 넘겨줘서 원본을 건드릴 수는 있지만 포인터 연산은 할 수 없는 형태로 클래스를 조작해준다.

```cpp
bool compare_circle(Circle& c, Circle& d) {

	if (c.getX() == d.getX()) {
		return  true;
	}

	return false;
}
```

그렇다면 왜 Call-By-Value를 기본으로 했을까? 객체 안에 또 다른 객체가 들어있는 경우에는 구현이 굉장히 어려울 수 있다. 이것을 프로그래머 레벨에서 구현하기 어렵기 때문에, 구현하기 쉬운 참조자 &를 option으로 제공해주고, Default Level에서 복사 기능을 제공해준다. 이로써 Call-By-Reference Level을 Option으로 제공한다.
