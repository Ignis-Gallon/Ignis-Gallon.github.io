---
title: "10주차(2): overriding, 다형성"
date: 2022-11-07
last_modified_at: 2022-11-27
category: major
tags: [객체기반SW설계, C++, overriding, 다형성, virtual]
---

# 10주차(2): overriding, 다형성

작성일 2022-11-07 · 최종 수정일 2022-11-27
{ .post-date }

## 멤버 함수의 재정의(method overriding)

- function overloading :

    함수의 이름이 같아도, parameter 개수, type, 순서가 다르면 다른 함수로 인식되는 것.  
    (return type은 상관 없음.)

- method overloading :

    클래스 정의에 적용된 function overloading

> overloading은 1개의 이름에 여러 개의 함수를 부착해서, 힘들게 만든다. (overload)

- overriding :

    클래스에만 적용되며, 상속받은 subclass에서 superclass의 method를 재정의하는 것을 overriding이라고 한다. OOP에서만 사용되는 개념이다. 자식 클래스에서 정의한 method가 동일한 signature(함수 이름, 파라미터)를 가진 부모 클래스의 method를 가려버리는 현상.

```cpp
#include <iostream>

using namespace std;

class Parent {
public:
	void whoareyou() {
		cout << "Parent" << endl;
	}
};

// 일반적으로 90% 이상은 상속받을 때 그냥 public inheritance를 사용한다.
// parent로부터 상속받아 child 클래스가 만들어진다.
class Child : public Parent {
public:
	void whoareyou() {
		cout << "Child" << endl;
	}
};

int main(void) {

	Child child;

	child.whoareyou();

	return 0;
}
```

→ 이와 같은 상황의 코드를 분석해보면, whoareyou method의 option은 1(Parent) 2(Child)가 있다. 2번에서 만들어진 whoareyou() method는 subclass안에 있는 method인데, 이것이 superclass의 whoareyou() method를 가려버린 것이다. 즉, 올라타 버린 것이다. (override)

```cpp
#include <iostream>

using namespace std;

class Parent {
public:
	void whoareyou() {
		cout << "Parent" << endl;
	}
};

// 일반적으로 90% 이상은 상속받을 때 그냥 public inheritance를 사용한다.
// parent로부터 상속받아 child 클래스가 만들어진다.
class Child : public Parent {
public:
	void whoareyou() {
		Parent::whoareyou();
		cout << "Child" << endl;
	}
};

int main(void) {

	Child child;
	child.whoareyou(); // child
	child.Parent::whoareyou(); 
	// kotlin은 따로 override라고 지정해서 함수를 지정한다음 써줘야 한다.
	// kotlin은 super method를 이용해 superclass의 기존 method를 실행한다.

	Parent parent;
	parent.whoareyou(); // parent

	return 0;
}
```

만약, subclass에서 superclass의 method를 불러주려면, namespace를 명시해서 불러주면 된다.

---

- 상속의 방법

public : public→public, protected→protected, private: 원래 못받음

protected : public→protected, protected→protected

private : public→private, protected→private

---

- 다중 상속(multiple inheritance)

    기존의 OOP에서는(소프트웨어 공학에서는) 다중 상속을 사용하지 않는 것이 좋으나, C++에서는 사용할 수밖에 없는 경우가 생긴다. Java에서는 이를 위배하지 않기 위해서 다중 상속을 제한하나, 대신 비슷한 기능을 할 수 있는 Interface 기능을 제공한다.

```cpp
#include <iostream>

using namespace std;

class A {
public:
	int x;
};

class B {
public:
	int x;
};

// multiple Inheritance
class C : public A, public B {

};

int main(void) {

	C cc;
	// cc.x = 30;
	cc.A::x = 10;
	cc.B::x = 20;

	return 0;
}
```

원래 객체지향 개념에서는 부모로부터 받은 것은 고유한 것이다. 그러나, 다중 상속을 통해 여러 개를 받게 구현할 수도 있다. 이는 소프트웨어 공학적으로 절대 좋은 방법이 아닌데, C++이 이렇게 구현할 수밖에 없었던 가슴 아픈 사연이 있다. 즉, C++은 다중상속이 가능하지만 superclass들이 같은 이름의 member나 variable을 가진다면 namespace를 명시해주어야 한다는 단점이 있다.

---

## Polymorphism (다형성)

⇒ 어떤 멤버 함수를 부를지, 그 때 그 때 상황에 따라 달라진다.

객체 지향에서 최고급 개념, 비유를 들자면 변신하는 사람들. 가면쓰고 그러면서 변신하는 사람들이 있기도 하고 친구들 중에서 감정의 기복이 심한 사람이 있는데 이런 것들을 Polymorphism이라고 한다. Polymorphism도 두 가지의 종류가 있는데, static polymorphism과 dynamic binding이 있다.

---

1. static polymorphism : method overriding

```cpp
#include <iostream>

using namespace std;

class Parent {
public:
	void whoareyou() {
		cout << "Parent" << endl;
	}
};

class Child : public Parent {
public:
	void whoareyou() {
		Parent::whoareyou();
		cout << "Child" << endl;
	}
};

int main(void) {

	Child child;
	child.whoareyou();
	child.Parent::whoareyou(); 

	Parent parent;
	parent.whoareyou(); // parent

	return 0;
}
```

superclass에서 method를 부르냐 subclass에서 method를 부르냐에 따라 결과가 달라지는 것을 의미한다. 즉, 자식에 대해서 동작할 때와 부모에 대해서 동작할 때 결과가 다른 것은 compile time에 이미 결정되기 때문에 static polymorphism이라고 한다.

---

<ol start="2"><li>dynamic binding</li></ol>

- upcasting

```cpp
#include <iostream>

using namespace std;

class Parent {
public:
	int x;

};

class Child : public Parent {
public:
	int y;

};

int main(void) {

	// 기존에는 이런 방식을 사용했었다.
	Parent* p = new Parent();
	delete p;

	Parent* p = new Child;

	p->x = 10;
	// p->y = 20; 애는 불가능하다.

	delete p;

	return 0;
}
```

내가 새로운 child 객체를 만들었는데, 얘는 반은 Parent고 반은 Child인데, 사실 child 안에는 Parent가 있으므로 Parent*로 가리킬 수 있게 된다. 이를 유식한 말로 upcasting이라고 한다.

- upcasting : superclass type의 pointer로 subclass를 가리키는 일.

superclass type의 pointer로는 subclass에 존재하는 superclass의 member와 method만 건드릴 수 있다. (compile error가 발생한다.)

- downcasting

```cpp
#include <iostream>

using namespace std;

class Parent {
public:
	int x;

};

class Child : public Parent {
public:
	int y;

};

int main(void) {

	// Child* c = new Parent(); 문법적으로 불가능하다.
	
	Parent* p = new Parent();
	Child* c = (Child*) p; 
	// typecasting을 이용하여 p를 Child로 바꿔버린다.
	// 이렇게 하면 합법이 되는데, 아래와 같은 문제가 발생할 수 있다.

	c->x = 50;
	// downcasting을 하면 실제로 값은 들어가지만, 그 영역은 내가 사용하지 않는
	// 남의 영역이므로 잘못된 error가 발생할 수 있다.
	// 따라서 downcasting은 class 사용에 대한 확신이 있어야만 사용할 수 있다.
	c->y = 999;

	return 0;
}
```

- 그렇다면, 이 문제는 해결할 수 없을까? C++은 이 문제를 해결하기 위해 부랴부랴 해결책을 내놓았다. 바로 dynamic cast이다.
- dynamic cast :

```cpp
#include <iostream>

using namespace std;

class Parent {
public:
	int x;

	virtual void showme() {

	}
};

class Child : public Parent {
public:
	int y;

};

int main(void) {
	
	Parent* p = new Parent();
	Child* c = (Child*) p; 
	// 이것은 y에 대한 공간이 잡혀있지 않는데, 
	// 값을 쓸 수는 있지만 다른 영역을 침범하는 것이므로 매우 위험하다.
	// 이를 막기 위해서 dynamic_cast를 이용한다.
	delete c;
	delete p;

	Child* c = dynamic_cast<Child*>(p);

	c->x = 50;
	// c->y = 999;
	
	return 0;
}
```

이렇게 downcasting을 이용하면 문제가 발생할 시에 exit code로 알려준다. 그럼 debug를 통해 찾을 수 있다.

downcasting은 그 자체로 논리적으로 문제가 많다. 실제로 코딩에서 사용하는 경우는 많지 않다. 반면, upcasting은 사용하는 경우가 자주 있다. downcasting을 이용하면 공간이 빵꾸가 나는 경우가 있지만, 이를 방지하기 위해 dynamic_cast와 virtual method를 사용한다. 

---

- 지금부터 하는 것은 polymorphism이다. 이전에 사용된 method overriding, static polymorphism을 뒤집는 개념이 나온다.

```cpp
#include <iostream>

using namespace std;

class Animal {
public:
	void speak() {
		cout << "Animal!" << endl;
	}
};

class Dog : public Animal {
public:
	void speak() {
		cout << "Dog!" << endl;
	}
};

int main(void) {

	Animal* a = new Animal();
	a->speak(); // Animal!

	Dog* d = new Dog();
	d->speak(); // Dog!

	delete a;
	delete d;

	Animal* a = new Dog(); // upcasting
	a->speak(); // Animal!

	return 0;
}
```

- 이제 위의 예제에서, Animal Class의 speak를 virtual로 만든다. 이렇게 하면, 결과로 Animal!이 아닌, Dog!가 출력된다.

```cpp
#include <iostream>

using namespace std;

class Animal {
public:
	virtual void speak() {
		cout << "Animal!" << endl;
	}
};

class Dog : public Animal {
public:
	void speak() {
		cout << "Dog!" << endl;
	}
};

int main(void) {

	Animal* a = new Dog(); // upcasting
	a->speak(); // Dog!
	delete a;

	return 0;
}
```

- Virtual : 혹시, 상속받은 자식 중에 overriding된 것이 있으면, 그것을 부르라는 것이다. 그러므로 superclass type의 pointer로 subclass를 가리키는 upcasting을 하게 되었을 때 한 method를 부르게 된다면, 그 때 그 method가 overriding되어 있고 superclass에서 virtual로 선언되어 있다면, superclass의 method(virtual로 선언된)가 아닌, subclass에 선언된 superclass의 method 중에서override한 method를 불러준다.
- dynamic binding : 어떤 speak()이 불릴지는 runtime()에 가봐야만 알 수 있다. upcasting과 virtual의 합동작품. ⇒ late binding이라고도 한다.

```cpp
#include <iostream>

using namespace std;

class Animal {
public:
	virtual void speak() {
		cout << "Animal!" << endl;
	}
};

class Dog : public Animal {
public:
	void speak() {
		cout << "Dog!" << endl;
	}
};

int main(void) {

	// 1
	Animal* a = new Dog(); // upcasting
	a->speak(); // Dog!
	
	// 2
	Dog d;
	Animal& aa = d; // reference type
	aa.speak(); // 얘도 역시 Dog!를 부른다.

	// 3
	Dog d2;
	Animal a2 = d2;
	a2.speak();

	return 0;
}
```

2번 예제

Reference도 Pointer와 비슷한 기능을 하지만, 주소 연산이 불가능한 것이다. 그러므로, 얘도 virtual의 영향을 받아서 Dog!를 불러온다.

3번 예제

얘는 Animal!을 부른다. 본인이 갖고 있는 객체 자체가 Animal의 speak만 가지게 되므로, Dog의 speak을 부를 수 없다.

---

```cpp
#include <iostream>

using namespace std;

class P2 {
public:
	virtual ~P2() {
		cout << "P2 소멸자" << endl;
	}
};

class C2 : public P2 {
public:
	int* p;
	C2() {
		p = new int[40];
	}
	~C2() {
		delete[] p;
		cout << "C2 소멸자" << endl;
	}
};

int main(void) {

	C2* c = new C2();
	delete c;
	// 여기까지는 아무런 문제가 발생하지 않는다.

	// upcasting, 얘는 합법이다. 그러나 문제가 있는 코드이다.
	P2* p = new C2();
	delete p;

	return 0;
}
```

```cpp
P2* p = new C2();
delete p;
```

이 코드는 문제가 발생한다. 문제가 뭐냐면, P2 Type의 Pointer로 C2를 upcasting 해주면, 얘는 C2의 소멸자를 부르지 못한다. 그러므로 memory leak이 발생한다. 따라서, P2의 소멸자에 virtual을 붙여서 밟고 지나갈 수 있도록 해줘서 P2의 소멸자를 밟고 지나가서 C2의 소멸자를 동작시켜준 뒤에 P2의 소멸자가 동작할 수 있도록 만들어준다.

> 따라서 virtual은 upcasting을 사용할 때만 사용하게 된다. 그래서 C++은 모든 소멸자에 virtual을 붙여줘야 안전하게 coding을 할 수 있다.
