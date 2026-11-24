---
title: "10주차(1): friend, 연산자 중복, 상속"
date: 2022-11-07
last_modified_at: 2022-11-20
category: major
tags: [객체기반SW설계, C++, friend, 연산자 오버로딩, 상속]
---

# 10주차(1): friend, 연산자 중복, 상속

작성일 2022-11-07 · 최종 수정일 2022-11-20
{ .post-date }

- friend function, friend class

```cpp
#include <iostream>
#include <vector>

using namespace std;

class A {
private:
	int a;

public:
	A(int _a) : a(_a) {

	}
	void setA(int _a) { // 음수 block을 적용해 safety를 높인다.

		if (_a < 0) {
			a = 0;
		}
		else {
			a = _a;
		}
	}
};

int main(void) {

	A obj(20);

	// cout << obj.a << endl;

	return 0;
}
```

class A의 member variable a는 private으로, 외부에서 접근이 불가능하다. main 함수에서 위와 같이 a를 호출할 수 없다는 것이다. 이게 당연한 것인데, 이를 유식한 말로 접근지정자(접근제어자) access specifier 라고 한다. 이는 OOP의 중요한 개념인 information hiding (정보은닉)과 관련이 있다. 

> information hiding은 code의 security & safety를 위해서 중요하다.  
> 예를 들어서, 은행의 계좌를 class로 만든다고 할 때, 가장 중요한 것은 address balance이다. 이를 만약에 public으로 선언한다고 하면 쉽게 접근할 수 있는 코드를 짤 수 있으므로, 중요한 member variable은 private으로 선언하여 내가 원하는 함수로만 지정해서 조작할 수 있도록 만드는 것이 원칙이다.

여기까지는 당연한건데, 지금부터 할 얘기는 이걸 깨버리는 얘기이다. 바로 friend function과 friend class이다.

---

```cpp
#include <iostream>
#include <vector>

using namespace std;

class A {
private:
	int a;

public:
	friend void showA(A _a);
	A(int _a) : a(_a) {

	}
	void setA(int _a) {

		if (_a < 0) {
			a = 0;
		}
		else {
			a = _a;
		}
	}
};

void showA(A _a) {
	_a.a = -666;
	cout << _a.a << endl;
}

int main(void) {

	A obj(20);

	// cout << obj.a << endl;

	return 0;
}
```

위의 코드처럼 함수 showA는 기본적으로 동작하지 않는 것이 원칙이지만, 동작하게 만들 수 있다. 어떻게 하냐면, class A의 public에 friend 함수를 선언해주면 된다. friend method는 class 내부에 선언한다. friend method는 class의 친구이기 때문에, 네 것과 내 것의 구분이 없다. 그냥 다 건드릴 수 있도록 만들어 준다. friend method에 대해서는 class 내에서 모든 것을 내어주도록 한다. 하지만 이는 OOP의 개념을 부정하고 파괴하는 것이므로, 사용하지 않는 것이 좋다.

```cpp
#include <iostream>
#include <vector>

using namespace std;

class A {
private:
	int a;

public:
	friend void showA(A _a);
	friend class B; // class B를 class A의 friend class로 선언한다.
	A(int _a) : a(_a) {

	}
	void setA(int _a) { // 음수 block을 적용해 safety를 높인다.

		if (_a < 0) {
			a = 0;
		}
		else {
			a = _a;
		}
	}
};

void showA(A _a) {
	_a.a = -666;
	cout << _a.a << endl;
}

class B {
public:
	B() {

	}
	void showA(A _a) {
		cout << _a.a << endl;
	}
};

int main(void) {

	A obj(20);

	// cout << obj.a << endl;

	return 0;
}
```

class B는 A의 friend class이지만, 이는 좋지 않은 방법이므로 사용하지 않는 것이 좋다.

---

- &lt;&lt;, &gt;&gt; 연산자의 중복.

```cpp
#include <iostream>

using namespace std;

class Point {
private:
	int x, y;
public:
	Point(int _x, int _y) : x(_x), y(_y) {

	}
	void showPoint() {
		cout << x << ", " << y << endl;
	}
};

int main(void) {

	Point p(10, 20);
	p.showPoint();

	cout << p;

	return 0;
}
```

re-direction 연산자는 friend를 이용하여 overloading해야 한다. 이것 대신에 그냥 출력해주는 함수를 하나 만들어서 사용하는 것이 더 좋다.

```cpp
#include <iostream>

using namespace std;

class Point {
private:
	int x, y;
public:
	Point(int _x, int _y) : x(_x), y(_y) {

	}
	void showPoint() {
		cout << x << ", " << y << endl;
	}
	friend ostream& operator<<(ostream& os, Point& p);
};

ostream& operator<<(ostream& os, Point &p) {
	os << p.x << ", " << p.y;
	// return os;
}

int main(void) {

	Point p(10, 20);
	p.showPoint();
	// cout << p << p;

	cout << p;

	return 0;
}
```

지금까지 operator overloading은 class 내에서 정의해주었지만, re-direction operator는 따로 함수로 지정해준다. class 내에 정의되지 않으므로, A &lt;&lt; B 에서 A, B를 둘다 전달해줘야 한다. ostream은 output stream이다. main에서 cout &lt;&lt; p &lt;&lt; p;와 같은 코드를 썼다고 했을 때, operator&lt;&lt; 에서 ostream&형으로 반환을 해주지 않으면 올바른 동작이 수행되지 않는다. (반환형 void같은 걸로 하면 안됨, cout &lt;&lt; p ⇒ ostream이 있어야 그 다음 &lt;&lt; p가 수행될 수 있음.)

```cpp
#include <iostream>

using namespace std;

class Point {
private:
	int x, y;
public:
	Point(int _x, int _y) : x(_x), y(_y) {

	}
	void showPoint() {
		cout << x << ", " << y << endl;
	}
	friend ostream& operator<<(ostream& os, Point& p);
	friend istream& operator>>(istream& os, Point& p);
};

// 지금까지 operator overloading은 class 내에서 정의해주었지만,
// re-direction operator는 따로 함수로 지정해준다.
// class 내에 정의되지 않으므로, A << B 에서 A, B를 둘다 전달해줘야 한다.
// ostream은 output stream이다.
ostream& operator<<(ostream& os, Point &p) {
	os << p.x << ", " << p.y;
	return os;
}
istream& operator>>(istream& in, Point& p) {
	in >> p.x >> p.y;
	return in;
}

int main(void) {

	Point p(10, 20);
	p.showPoint();

	cout << p;
	// cout << p << p;

	cin >> p; // 이것을 하기 위해서 friend 함수를 이용

	return 0;
}
```

> operator overloading은 기존 연산자를 재정의하는 것이므로, 새로운 연산자를 만들수는 없음.

---

- 상속 : 생산성을 위한 필수 개념

```cpp
#include <iostream>

using namespace std;

class A {
public:
	int a, b;

public:
	A() {
		a = 10;
		b = 20;
	}
	void showAB() {
		cout << a << ", " << b << endl;
	}
};

class AA : public A {
	// AA는 아무런 데이터가 없더라도 A로부터 상속받은 a, b가 있다.
public:
	int c;

public:
	void showABSum() {
		cout << (a + b) << endl;
	}
};

class AAA : public AA {
	
};

int main(void) {

	AAA aaa;
	aaa.showAB();
	aaa.showABSum();
	aaa.a = 20;
	aaa.b = 30;
	aaa.c = 100;

	AA aa; // default 생성자는 ()로 부르지 않는다.
	cout << aa.a << ", " << aa.b << endl;
	aa.showAB();
	aa.showABSum();

	return 0;
}
```

---

public은 무조건 상속된다.  
private은 넘겨주지 않는다. 상속이 불가능하다.  
protected는 상속된다. 단, 외부에 대해서는 private으로 동작한다.

```cpp
#include <iostream>

using namespace std;

class A {
protected:
	int a, b;

public:
	A() {
		a = 10;
		b = 20;
	}
	void showAB() {
		cout << a << ", " << b << endl;
	}
};

class AA : public A {
	// AA는 아무런 데이터가 없더라도 A로부터 상속받은 a, b가 있다.
public:
	int c;

public:
	void showABSum() {
		cout << (a + b) << endl;
	}
};

class AAA : public AA {
	
};

int main(void) {

	AAA aaa;
	aaa.showAB();
	aaa.showABSum();
	// a, b는 호출이 불가능하다. 왜냐면, protected이기 때문.
	// aaa.a = 20;
	// aaa.b = 30;
	// aaa.c = 100;

	AA aa; // default 생성자는 ()로 부르지 않는다.
	aa.showAB();
	aa.showABSum();

	return 0;
}
```

---

- 상속에서의 생성자와 소멸자

```cpp
#include <iostream>

using namespace std;

class A {
public:
	int a, b;

public:
	A() {
		cout << "부모 생성자\n";
		a = 10;
		b = 20;
	}
	void showAB() {
		cout << a << ", " << b << endl;
	}
	~A() {
		cout << "부모 소멸자\n";
	}
};

class AA : public A {
	// AA는 아무런 데이터가 없더라도 A로부터 상속받은 a, b가 있다.
public:
	int c;

public:
	AA() {
		cout << "자식 생성자\n" << endl;
	}
	void showABSum() {
		cout << (a + b) << endl;
	}
	~AA() {
		cout << "자식 소멸자\n";
	}
};

int main(void) {

	AA aa;

	return 0;
}
```

만약 aa를 호출한다면, 부모 생성자가 먼저 호출되고 그 후에 자식 생성자가 호출된다. 새로운 클래스를 생성할 때는 메모리를 할당받아야 하는데, 기존에 메모리가 잡혀있는 부모 클래스의 변수들부터 메모리가 잡히게 된다. 소멸자는 생성자의 역순으로 실행된다.
