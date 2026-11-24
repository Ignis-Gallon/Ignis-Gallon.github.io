---
title: "11주차 (1): 순수 가상 함수, 파일 입출력"
date: 2022-11-14
last_modified_at: 2022-11-20
category: major
tags: [객체기반SW설계, C++, 순수 가상 함수, 추상 클래스, 파일 입출력]
---

# 11주차 (1): 순수 가상 함수, 파일 입출력

작성일 2022-11-14 · 최종 수정일 2022-11-20
{ .post-date }

> polymorphism, 다형성  
>
> upcasting  
>
> Dynamic binding, virtual

- OOP 1. information hiding (정보 은닉)  
    ⇒ access specifier : private, protected, public 
- OOP 2. : Inheritance : for reusability (코드 재사용성) → 생산성이 증가  
    → protected (상속이 없으면 큰 의미가 없음.) : child 에게는 public, 외부에는 private으로 사용.
- binding : 내가 수행할 메서드 중 누굴 부를지 결정하는 문제. (어느 함수를 부를 것인가)  
    → 컴파일 타임에 결정되면 static binding, 실행 중에 결정되면 dynamic binding

Method(function) overloading  
Method overriding : 부모의 method와 똑같이 생겼을 때 method overriding이 가능하다.  
→ 자식의 method가 부모의 것을 가리게 되므로, 우선 순위는 자식의 것이 된다.  
이 문제는 static binding이다.

- upcasting : 자식 객체들을 부모 클래스 포인터로 가리킬 때. (합법)
- downcasting : 부모 객체를 자식 클래스 포인터로 가리킬 때. (부분적 합법)
- Dynamic binding : virtual (method 앞에 붙는다. 실행 중에 binding이 결정됨)

```cpp
#include <iostream>

using namespace std;

class A {
public:
	void showMe() {
		cout << "parent" << endl;
	}
};

class B : public A {
public:
	void showMe() { // method overriding
		cout << "child" << endl;
	}
};

int main(void) {

	B* pb = new B();
	A* pa = nullptr;

	pa = pb; // upcasting 
	pa->showMe(); // parent pointer only points it's own.
	pb->showMe(); 

	delete pb;

	return 0;
}
```

- virtual을 추가한다.

```cpp
#include <iostream>

using namespace std;

class A {
public:
	virtual void showMe() {
		cout << "parent" << endl;
	}
};

class B : public A {
public:
	void showMe() { // method overriding
		cout << "child" << endl;
	}
};

int main(void) {

	B* pb = new B();
	A* pa = nullptr;

	pa = pb; // upcasting 
	pa->showMe(); // parent pointer only points it's own.
	pb->showMe();

	delete pb;

	return 0;
}
```

- virtual을 사용하면, upcasting 상황에서도 부모 class의 method를 밟고 넘어가서 자식 class에서 overriding된 method를 불러준다. 이는 컴파일 타임에 결정될 수 없으며, 실제로 실행되는 상황에서 쫓아가며 결정된다.  
    → virtual이 붙은 방식을 dynamic binding 이라고 한다.

> polymorphism은 어떤 method를 호출할 지 결정하는 과정이며, static binding은 compile time에 결정이 되지만, dynamic binding은 실행이 되는 과정에서 결정된다.

---

## 순수 가상 함수(pure virtual method)

순수 가상 함수는 Java의 Interface의 역할을 한다.  
위의 코드에서 나온 showMe()는 순수 가상 함수가 아니다. 왜냐면, 실체가 존재하기 때문이다. 그냥 가상 함수일 뿐이다. 순수 가상 함수는 실체 (구현체)가 없는 것이다.

순수해지려면, 구현체를 없애면 된다.

```cpp
#include <iostream>

using namespace std;

class A {
public:
	virtual void showMe() = 0;
};

class B : public A {
public:
	void showMe() {
		
	}
};

int main(void) {

	return 0;
}
```

- pure virtual function은 그 자체로 함수에 구현이 없기 때문에, pure virtual method를 가진 클래스를 특별히 abstract class(추상 클래스)라고 하며, abstract class로부터는 객체를 만들어낼 수 없다. 그렇다면 이것이 왜 필요할까?

ex) 만약 코딩을 하는데, 모든 클래스가 공통적으로 반드시 특정 method를 가지도록 설계하고 싶다. (실수로 넣지 않는 경우와 같은 오류를 없애고 싶다.) 이런 경우, abstract class인 virtual class를 만든다. 

```cpp
#include <iostream>

using namespace std;

class A {
public:
	virtual void showMe() = 0;
};

class B : public A {
public:

};

int main(void) {

	B b;

	return 0;
}
```

- 이런 식으로 만든다면, B의 instance를 만들 수 없게 된다. 왜냐면, abstract class A의 overrider를 만들지 않았기 때문이다.

```cpp
#include <iostream>

using namespace std;

class Triangle {
private:
	int width, height;
public:
	Triangle(int _w, int _h) : width(_w), height(_h) {
	}
	double getArea() {
		return (double)(0.5 * width * height);
	}
};

class Circle {
private:
	double r;
public:
	Circle(double _r) : r(_r) {
	}
	double getArea() {
		return (double)(3.14 * r * r);
	}
};

class Rectangle {
private:
	int width, height;
public:
	Rectangle(int _w, int _h) : width(_w), height(_h) {
	}
	double getArea() {
		return (double)(width * height);
	}
};

int main(void) {

	return 0;
}
```

이 경우, 개발자가 잊지 않고 각 도형 class에 대하여 모두 getArea()를 만들어주었지만 개발자가 실수로 구현을 하지 않거나 이름 사이에 get_Area()처럼 under bar를 넣는 등 하면, 문제가 발생할 수 있다. 이를 대비하기 위해 abstract class를 상속해준다.

```cpp
#include <iostream>

using namespace std;

class Shape {
	virtual double getArea() = 0;
};

class Triangle : public Shape {
private:
	int width, height;
public:
	Triangle(int _w, int _h) : width(_w), height(_h) {
	}
	double getArea() {
		return (double)(0.5 * width * height);
	}
};

class Circle : public Shape {
private:
	double r;
public:
	Circle(double _r) : r(_r) {
	}
	double getArea() {
		return (double)(3.14 * r * r);
	}
};

class Rectangle : public Shape {
private:
	int width, height;
public:
	Rectangle(int _w, int _h) : width(_w), height(_h) {
	}
	double getArea() {
		return (double)(width * height);
	}
};

int main(void) {

	Triangle t(10, 10);
	Circle c(10.0);
	Rectangle r(10, 10);

	return 0;
}
```

class Shape()은 Java의 interface 개념이다. C++에서는 abstract class, virtual function으로 이용한다. 

- interface : 클래스가 반드시 구현해야 할 메소드들의 목록

인터페이스를 이용하면, 이 인터페이스를 구현하는 모든 클래스를 마치 하나처럼 다룰 수 있게 된다.  

```cpp
#include <iostream>

using namespace std;

class Shape {
public:
	virtual double getArea() = 0;
};

class Triangle : public Shape {
private:
	int width, height;
public:
	Triangle(int _w, int _h) : width(_w), height(_h) {
	}
	double getArea() {
		return (double)(0.5 * width * height);
	}
};

class Circle : public Shape {
private:
	double r;
public:
	Circle(double _r) : r(_r) {
	}
	double getArea() {
		return (double)(3.14 * r * r);
	}
};

class Rectangle : public Shape {
private:
	int width, height;
public:
	Rectangle(int _w, int _h) : width(_w), height(_h) {
	}
	double getArea() {
		return (double)(width * height);
	}
};

int main(void) {

	Triangle t(10, 10);
	t.getArea();
	Circle c(10.0);
	c.getArea();
	Rectangle r(10, 10);
	r.getArea();

	Shape* s[3];
	s[0] = &t;
	s[1] = &c;
	s[2] = &r;

	for (int i = 0; i < 3; i++) {
		s[i]->getArea(); // Shape 클래스에서 public: 선언안해주면 접근 불가능.
	}

	return 0;
}
```

객체는 그 내부에 속한 interface에 따라서 다른 type으로 접근할 수 있게 된다. 객체는 인터페이스를 여러 개 구현할 수 있다. Java에서는 only one class inheritance이고 여러 개의 interface를 구현할 수 있다. 하지만 C++에서는 interface라는 개념이 없고 abstract class를 이용하여 interface의 기능을 구현하기 때문에 다중 상속을 받아야만 한다.

---

## 파일 입출력

stream : 외부에 있는 데이터를 읽고 쓰는 통로

→ input stream : file에서 data를 가져오는 것

→ output stream : file에 data를 쓰는 것

- 파일의 두 가지 종류 : text, binary

    1. text : ascii code 값으로 저장 ⇒ 0이 ascii code 값인 48이 저장됨
    2. binary : 값 그대로 ⇒ 0이 그대로 저장됨

```cpp
#include <iostream>
#include <fstream> // file header

using namespace std;

int main(void) {

	{
		// output file stream class
		ofstream os("test.txt");

		// cout : console out. 기본적으로 제공되는 객체
		for (int i = 0; i < 100; i++) {
			os << i << " ";
		}
	}
	
	ifstream is("test.txt");
	
	int number; // int 단위로 값을 읽어온다.
	while (is >> number) {
		cout << number;
	}

	return 0;
}
```

ofstream의 소멸자에 close가 존재하기 때문에, 굳이 close를 직접 해주지 않아도 된다. 위와 같은 방법으로 file을 아주 간단하게 읽고 쓸 수 있다.

```cpp
#include <iostream>
#include <fstream> // file header

using namespace std;

int main(void) {

	{
		// output file stream class
		ofstream os("test.txt");

		// cout : console out. 기본적으로 제공되는 객체
		for (int i = 0; i < 100; i++) {
			os << i << " ";
		}
	}
	
	{
		ifstream is("test.txt");

		int number; // int 단위로 값을 읽어온다.
		while (is >> number) {
			cout << number;
		}
	}

	{
		ifstream is("test.txt");

		char c;

		while (!is.eof()) {
			is.get(c); 
			// ifstream 내에 존재하는 get method를 이용하여
			// char를 하나 읽어온다.
			
			cout << c << endl;
		}
	}

	return 0;
}
```

file을 읽고 쓰는 것도 객체를 이용하며 객체의 method를 이용해 접근할 수도 있다.
