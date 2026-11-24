---
title: "9주차: static 변수와 함수, Singleton"
date: 2022-10-31
last_modified_at: 2022-11-27
category: major
tags: [객체기반SW설계, C++, static, Singleton, 연산자 오버로딩]
---

# 9주차: static 변수와 함수, Singleton

- 정적 변수 : static variable

```cpp
#include <iostream>

using namespace std;

class MyObj {
private:
	int i;
	float j;
public:
	MyObj(int _i, float _j) : i(_i), j(_j) {

	}
	void showMyContents() {
		cout << "i : " << i << ", j : " << j << endl;
	}
};

int main(void) {

	

	return 0;
}
```

→ 아주 평범한 프로그램, 우리가 여기서 MyObj Class에서 객체를 만든다. 이 객체를 Instance 혹은 Object라고 한다. Object를 선언한 뒤, Object를 구성하는 코드 자체는 서로 다른 Object 끼리 공유할 수도 있지만, 그 안에 들어있는 멤버 변수들의 메모리 공간은 공유하지 않는다.

```cpp
#include <iostream>

using namespace std;

class MyObj {
private:
	int i;
	float j;
	static int k;
public:
	MyObj(int _i, float _j) : i(_i), j(_j) {

	}
	void showMyContents() {
		cout << "i : " << i << ", j : " << j << endl;
		cout << "k : " << k << endl;
	}
};

int MyObj::k = 10; // static variable의 초기화

int main(void) {

	MyObj o1(10, 3.14);

	return 0;
}
```

여기서는 클래스 내에 static variable k를 추가한다. k는 이 class로 부터 만들어지는 모든 Instance(Object)에서 변수 k를 한 copy만 갖고 공유해서 쓰는 변수이다. 이를 static variable이라고 한다. static variable은 Instance(Object)가 없어도 존재하는, 신적인 존재이다. 만약 Object끼리 어떤 상태를 공유하거나 할 때 굉장히 유용하게 사용된다.

```cpp
#include <iostream>

using namespace std;

class MyObj {
private:
	int i;
	float j;
public:
	static int k;
public:
	MyObj(int _i, float _j) : i(_i), j(_j) {

	}
	void showMyContents() {
		cout << "i : " << i << ", j : " << j << endl;
		cout << "k : " << k << endl;
	}
};

int MyObj::k = 10; // static variable의 초기화

int main(void) {

	MyObj o1(10, 3.14); // 정적 할당
	MyObj* o2 = new MyObj(20, 9.999); // 동적 할당

	o1.showMyContents(); // 10, 3.14, 10
	o2->k = 777;
	o1.showMyContents(); // 10, 3.14, 777

	return 0;
}
```

- 정적 함수 : static function

```cpp
#include <iostream>

using namespace std;

class MyObj {
private:
	int i;
	float j;
public:
	static int k;
public:
	MyObj(int _i, float _j) : i(_i), j(_j) {

	}
	void showMyContents() {
		cout << "i : " << i << ", j : " << j << endl;
		cout << "k : " << k << endl;
	}
	static void showStatics() {
		cout << "I am a static function, method\n";
		// cout << i << " " << j << endl; // 호출 불가.
		cout << k << endl; // static variable이므로, 호출 가능.
	}
};

int MyObj::k = 10; // static variable의 초기화

int main(void) {

	MyObj::showStatics();

	return 0;
}
```

static function(method)도 static variable과 마찬가지로, Instance가 없어도 호출할 수 있는 함수이다. 그러나, 제약사항이 존재한다. 바로, static이 아닌 변수들을 호출할 수 없다. 왜냐면, 객체가 있어야만 존재할 수 있는 변수들과 함수들은 호출할 수 없게 된다.

전역 변수는 사용하면 안되는 변수 중의 하나이다. 그런데, C++로 넘어오면서는 전역 변수는 포기해야 한다. 왜냐면, 만약에 전역 변수를 두게 되면 전역 변수를 활용하는 함수들은 전역 변수가 없으면 쓸 수 없다는 문제가 발생한다. Ctrl+C Ctrl+V를 하면 동작하지 못한다. 그래서, 코드의 재활용성이 굉장히 떨어지므로, 좋은 프로그램이 되지 못한다. 전역 변수라는 것은 C언어에서만 활용하는 개념으로 남겨두어야 한다. (C언어에서는 전역 변수가 없으면 짤 수 없는 코드들도 있다.)

> Singleton Design Pattern (디자인 패턴)  
>
> 디자인 패턴이란? (참고 서적 : Design Patterns, Elements of Reusable OO S/W)
>
> gof : Gang of Four (네 명의 강도들)  
>
> 게임을 만들거나, 프로그램을 만들 때 모두 사용되는 프로그래밍 패턴들을 집대성한 책이 바로 이 책이다.  
>
> 상황 : 어떻게 코드를 짜야할 지에 대한 문제 상황이 제시 되었다. Singleton Pattern의 문제 상황은 이와 같다.  
>
> Q. 특정 클래스에서 오직 1개의 객체만 만들어지도록 할 수 있을까?  
> 게임 같은 곳에서 보스와 같은 객체들, 혹은 네트워크에서 한 개의 클래스를 공유해야 하는 상황들에서 주로 사용될 수 있다.

클래스로부터 오직 1개의 객체만 만들어지도록 하는 싱글톤 패턴이 적용된 클래스

```cpp
#include <iostream>

using namespace std;

class MyOnlyObj {
public:
	static MyOnlyObj* getInstance() {
		if (instance == nullptr) {
			instance = new MyOnlyObj();
		}

		return instance;
	}
	void showD() {
		cout << "D : " << d << endl;
	}
	void setD(int _d) {
		this->d = _d;
	}

private:
	int d;
private:
	static MyOnlyObj* instance;

	MyOnlyObj() {

	}
};

MyOnlyObj* MyOnlyObj::instance = nullptr; // static 변수는 처음에 무조건 초기화를 해주어야 한다.

int main(void) {

	MyOnlyObj* p1 = MyOnlyObj::getInstance();
	MyOnlyObj* p2 = MyOnlyObj::getInstance();
	MyOnlyObj* p3 = MyOnlyObj::getInstance();

	

	return 0;
}
```

main 함수에서, p1, p2, p3는 모두 같은 하나의 Instance를 가리키고 있다. 생성자가 private에 들어가 있으므로, getInstance를 제외한 다른 방식으로는 변수의 Instance를 생성하거나 참조할 수 없다.

만약 게임을 만들 때 어떤 캐릭터가 only one으로 만들어져야 한다면, 하나의 Instance만 항상 얻어서 사용할 수 있게 된다. 

---

- Operator Overloading

```cpp
#include <iostream>

using namespace std;

class MyVector {
private:
	int x, y;

public:
	MyVector(int _x, int _y) : x(_x), y(_y) {

	}
	void showXY() {
		cout << "x : " << x << ", y : " << y << endl;
	}
	MyVector operator+ (MyVector& other) {
		MyVector temp(this->x + other.x, this->y + other.y);
		return temp;
	}
	bool operator== (MyVector& other) {
		return ((this->x == other.x) && (this->y == other.y));
	}

	/*
	MyVector addTwoVectors(MyVector& v) {
		MyVector temp(this->x + v.x, this->y + v.y);
		return temp;
	}
	*/
};

int main(void) {

	MyVector v1(10, 10);
	MyVector v2(20, 20);

	// MyVector v3 = v1.addTwoVectors(v2);
	MyVector v3 = v1 + v2; // v3 (30, 30)
	v3.showXY();

	cout << boolalpha;
	cout << "Same? " << (v1 == v2) << endl;

	return 0;
}
```

Operator Overloading(연산자 오버로딩)은, 클래스간의 사칙 연산을 custom으로 만들어주는 것이다. 만약 연산자 오버로딩을 지정해주지 않는다면, 위의 코드에서 method로 만든 addTwoVectors와 같이 구현해주어야 한다.

C++에서 연산자 오버로딩 메소드를 만들려면, 앞에 operator를 쓰고 바로 뒤에 사용할 기호를 쓰면 된다. 원하는 기호를 막 사용해도 된다.

- 다양한 타입의 operator overloading

```cpp
#include <iostream>

using namespace std;

class MyVector {
private:
	int x, y;

public:
	MyVector(int _x, int _y) : x(_x), y(_y) {

	}
	void showXY() {
		cout << "x : " << x << ", y : " << y << endl;
	}
	MyVector operator+ (MyVector& other) {
		MyVector temp(this->x + other.x, this->y + other.y);
		return temp;
	}
	bool operator== (MyVector& other) {
		return ((this->x == other.x) && (this->y == other.y));
	}

	/*
	MyVector addTwoVectors(MyVector& v) {
		MyVector temp(this->x + v.x, this->y + v.y);
		return temp;
	}
	*/
};

int main(void) {

	MyVector v1(10, 10);
	MyVector v2(20, 20);

	// MyVector v3 = v1.addTwoVectors(v2);
	MyVector v3 = v1 + v2; // v3 (30, 30)
	v3.showXY();

	cout << boolalpha;
	cout << "Same? " << (v1 == v2) << endl;

	return 0;
}
```

```cpp
#include <iostream>

using namespace std;

class MyVector {
private:
	int x, y;

public:
	MyVector(int _x, int _y) : x(_x), y(_y) {

	}
	void showXY() {
		cout << "x : " << x << ", y : " << y << endl;
	}
	MyVector operator+ (MyVector& other) {
		cout << "첫 번째 더하기 오버로딩\n";
		MyVector temp(this->x + other.x, this->y + other.y);
		return temp;
	}
	MyVector* operator+ (MyVector* other) {
		cout << "두 번째 더하기 오버로딩\n";
		MyVector* temp = new MyVector(this->x + other->x, this->y + other->y);
		return temp;
	}
	bool operator== (MyVector& other) {
		return ((this->x == other.x) && (this->y == other.y));
	}
};

int main(void) {

	MyVector* v1 = new MyVector(10, 10);
	MyVector v2(20, 20);

	// MyVector v3 = v1 + v2;
	MyVector v3 = *v1 + v2;

	return 0;
}
```

연산자 오버로딩은 기본 타입에 대해서는 재정의 할 수없다.  
내가 재정의 할 수 있는 것은 Class에 대해서만 할 수 있다.  
따라서, object간의 연산에서는 반드시 객체가 들어와야만 한다.  
포인터나 기본 자료형이 들어와서는 안된다.

---

```cpp
#include <iostream>

using namespace std;

class MyVector {
private:
	int x, y;

public:
	MyVector(int _x, int _y) : x(_x), y(_y) {

	}
	void showXY() {
		cout << "x : " << x << ", y : " << y << endl;
	}
	MyVector operator+ (MyVector& other) {
		MyVector temp(this->x + other.x, this->y + other.y);
		return temp;
	}
	MyVector* operator+ (MyVector* other) {
		MyVector* temp = new MyVector(this->x + other->x, this->y + other->y);
		return temp;
	}
	bool operator== (MyVector& other) {
		return ((this->x == other.x) && (this->y == other.y));
	}
	bool operator!= (MyVector& v) {
		return !(*this == v); 
		// 이런 식으로 이미 클래스 내에서 정의된 operator overloading을 활용하여
		// 다른 operator overloading으로 만들 수 있다.
	}
};

int main(void) {

	MyVector v1(10, 20);
	MyVector v2(20, 20);

	cout << boolalpha;
	cout << "v1 == v2\n" << (v1 == v2) << endl;

	return 0;
}
```

- Increment Operator

```cpp
#include <iostream>

using namespace std;

class MyValue {
private:
	int x;
public:
	MyValue(int _x) : x(_x) {

	}
	/*void operator++() {
		this->x = this->x + 1;
		return;
	}*/
	MyValue& operator++ () {
		this->x = this->x + 1;
		return *this;
	}
	void showMyValue() {
		cout << "My Value : " << this->x << endl;
	}
};

int main(void) {

	MyValue v1(10);
	++(++v1);
	v1.showMyValue();

	return 0;
}
```

- 클래스에서 접근 제어자를 설정하지 않으면, default는 private이다.

++(++v1); 을 할 경우, 오류가 발생하는데 그 이유는 기존의 operator overloading이 아무것도 return하지 않기 때문이다. 따라서 이를 MyVector (자기 자신)를 return하도록 만들어 준다.

그런데, 그냥 단순히 MyVector를 return하도록 만들면, 값이 제대로 증가되지 않는다. 왜냐면, ++(++v); 는 복사본이 return된 것을 증가시켜 주는 것이기 때문에, 원본이 증가되지 않고 복사본이 증가된다. 그렇기 때문에 참조자(reference)를 이용하여 원본을 넘겨주도록 한다.

```cpp
#include <iostream>

using namespace std;

class MyValue {
private:
	int x;
public:
	MyValue(int _x) : x(_x) {

	}
	/*void operator++() {
		this->x = this->x + 1;
		return;
	}*/
	/*MyValue operator++ () {
		this->x = this->x + 1;
		return *this;
	}*/

	// prefix
	MyValue& operator++ () {
		this->x = this->x + 1;
		return *this;
	}

	// post fix
	MyValue operator++ (int) {
		MyValue temp = *this;
		this->x = this->x + 1;

		return temp;
	}

	void showMyValue() {
		cout << "My Value : " << this->x << endl;
	}
};

int main(void) {

	MyValue v1(10);
	++(++v1);
	v1.showMyValue();

	++v1;
	++v1;

	v1.showMyValue();

	return 0;
}
```

- prefix를 했으므로, postfix를 만들어준다.

후위증가연산은 전위증가연산과 달리 parameter에 int를 써준다. 후위증가연산은 먼저 기존의 값을 return해줘야 하며, 그 후에 원본의 값을 증가 시켜주어야 하므로, 복사본을 만들어놓은 다음에 원본을 증가시키고 복사본을 return한다.

```cpp
#include <iostream>

using namespace std;

class MyValue {
private:
	int x;
public:
	MyValue(int _x) : x(_x) {

	}
	/*void operator++() {
		this->x = this->x + 1;
		return;
	}*/
	/*MyValue operator++ () {
		this->x = this->x + 1;
		return *this;
	}*/

	// prefix
	MyValue& operator++ () {
		this->x = this->x + 1;
		return *this;
	}

	// post fix
	MyValue operator++ (int) {
		MyValue temp = *this;
		this->x = this->x + 1;

		return temp;
	}

	void showMyValue() {
		cout << "My Value : " << this->x << endl;
	}
};

int main(void) {

	MyValue v1(10);
	++(++v1);
	v1.showMyValue();

	++v1;
	++v1;

	v1.showMyValue();

	MyValue k = v1++;

	cout << "k : " << endl;
	k.showMyValue();

	cout << "v1 : " << endl;
	v1.showMyValue();

	return 0;
}
```

temp는 이 함수에서 사라지게 되므로, reference type으로 넘겨주게 되면 오류가 발생한다. 그러나 temp가 사라지게 되더라도, return될 때 다시 복사본이 넘어가기 때문에 안전하게 넘어간다고 판단할 수 있다.

```cpp
#include <iostream>

using namespace std;

class MyValue {
private:
	int x;
public:
	MyValue(int _x) : x(_x) {

	}

	// prefix
	MyValue& operator++ () {
		this->x = this->x + 1;
		return *this;
	}

	// post fix
	MyValue operator++ (int) {
		MyValue temp = *this;
		this->x = this->x + 1;

		return temp;
		// temp는 이 함수에서 사라지게 되므로,
		// reference type으로 넘겨주게 되면 오류가 발생한다.
		// 그러나 temp가 사라지게 되더라도, return될 때 다시 복사본이 넘어가기 때문에
		// 안전하게 넘어간다고 판단할 수 있다.
	}

	void showMyValue() {
		cout << "My Value : " << this->x << endl;
	}
};

int main(void) {

	MyValue v1(10);

	++(v1++); // 11

	(++v1)++; // 위에 걸 안하면 12, 위에 걸 하면 13.

	return 0;
}
```

++(v1++); // 11

넘어온 복사본을 증가시켜 주는 것이므로, 원본은 11이 된다.

(++v1)++; // 12

원본이 넘어오고, 이를 증가시킨 뒤 복사본을 다시 넘겨주는 것이므로 12가 된다.

> 이런 방식의 코딩 스타일은 좋지 않다. 이런 것을 사용하지 않고 그냥 두 줄로 풀어서 사용하면 된다.

---

- 대입 연산자

```cpp
#include <iostream>

using namespace std;

class MyValue {
private:
	int x;
public:
	MyValue(int _x) : x(_x) {

	}

	// prefix
	MyValue& operator++ () {
		this->x = this->x + 1;
		return *this;
	}

	// post fix
	MyValue operator++ (int) {
		MyValue temp = *this;
		this->x = this->x + 1;

		return temp;
	}

	void showMyValue() {
		cout << "My Value : " << this->x << endl;
	}
};

int main(void) {

	MyValue v1(10);
	MyValue k = v1; // 복사 생성자

	MyValue v2(20);
	k = v2; // 대입 연산자, assignment operator
	// default assignment operator가 들어간다.

	return 0;
}
```

만약, default assignment operator를 사용하지 않고, 내가 원하는 동작을 구현하고 싶다면 따로 정의해주어야 한다. 만약, 대입 연산자를 사용하면 x의 값을 항상 2배씩 증가시켜주는 동작을 구현한다고 해보자.

```cpp
#include <iostream>

using namespace std;

class MyValue {
private:
	int x;
public:
	MyValue(int _x) : x(_x) {

	}

	// prefix
	MyValue& operator++ () {
		this->x = this->x + 1;
		return *this;
	}

	// post fix
	MyValue operator++ (int) {
		MyValue temp = *this;
		this->x = this->x + 1;

		return temp;
	}

	// 대입 연산자
	MyValue& operator= (const MyValue& other) {
		 this->x = 2 * other.x;
		 return *this;
	}
	/*void operator= (const MyValue& other) {
		this->x = 2 * other.x;
		return *this;
	}*/

	void showMyValue() {
		cout << "My Value : " << this->x << endl;
	}
};

int main(void) {

	MyValue v1(10);
	MyValue k = v1; // 복사 생성자

	MyValue v2(20);
	k = v2; // 대입 연산자, assignment operator
	// default assignment operator가 들어간다.
	// k = v2 = v1;

	k.showMyValue();

	return 0;
}
```

사실 void를 해도 큰 문제는 없는데, k = v2 = v1; 과 같이 chaining에 대한 대비를 해주기 위하여 MyValue&를 return해주도록 한다. 그런데 왜 하필 &를 붙여야 하는가? 사실 &를 붙이지 않고 복사본을 넘겨주어도 원래 동작과 같은데, 왜 reference를 쓰나면, 효율성 때문이다. 만약 이 동작을 매우 많이 수행한다고 할 때, 당연히 복사본을 넘기는 것이 시간이 많이 소요될 것이다. 그렇기 때문에 효율성을 위해 C++에서는 꼭 복사본을 넘겨야 하는 경우가 아니라면 반드시 &를 붙여주도록 한다.

---

- Index Operator

```cpp
#include <iostream>

using namespace std;

class MyValue {
private:
	int x;
public:
	MyValue(int _x) : x(_x) {

	}

	// prefix
	MyValue& operator++ () {
		this->x = this->x + 1;
		return *this;
	}

	// post fix
	MyValue operator++ (int) {
		MyValue temp = *this;
		this->x = this->x + 1;

		return temp;
	}

	// 대입 연산자
	MyValue& operator= (const MyValue& other) {
		this->x = 2 * other.x;
		return *this;
	}

	// index operator overloading
	int operator[](int _k) {
		if (_k < 50) {
			return -999;
		}
		else {
			return 999;
		}
	}

	void showMyValue() {
		cout << "My Value : " << this->x << endl;
	}
};

int main(void) {

	MyValue v1(10);

	cout << v1[30] << endl; // -999

	return 0;
}
```

이 코드는 예시를 들기 위해 단적으로 코딩을 했지만, 실제로 사용할 때는 객체 내에 존재하는 배열의 원소를 원하는 대로 꺼내올 수 있다. 

---

- 포인터 연산자의 중복(pointer operator overloading)

```cpp
#include <iostream>

using namespace std;

/*

포인터 연산자의 중복
pointer operator overloading

*/

int main(void) {

	while (true) {

		int* p = new int(40);
		// delete p;
	}

	return 0;
}
```

만약, delete p를 하지 않는다면, 해제가 안되기 때문에 메모리가 부족해져서 bad_alloc 오류가 발생한다. 그렇기 때문에 반드시 delete를 해줘야 한다.

delete를 까먹을 것 같으면, unique_ptr을 사용해보자.

```cpp
#include <iostream>

using namespace std;

/*

포인터 연산자의 중복
pointer operator overloading

*/

int main(void) {

	while (true) {

		unique_ptr<int> p(new int(40));
		// p라는 포인터가 4byte가 할당된 메모리를 가리키게 된다.
		// 내부적으로 할당과 해제를 자동으로 처리해준다.

		// int* p = new int(40);
		// delete p;
	}

	return 0;
}
```

이전의 코드와 unique_ptr의 기능은 같은 기능이다. 단, 메모리를 해제할 필요가 없다는 강점이 있다. 자바는 가비지 컬렉터를 쓰는데, 가끔씩 가비지 컬렉터가 오동작을 하게 되면 해제해야 하는 포인터를 놓쳐서 기존의 동작이 느려지는 경우가 발생하고는 한다.

> 그런데, smart pointer(unique_ptr)을 어떻게 만들었을까? 이걸 만드는 방법은 바로 operator overloading, 즉, 포인터 연산자의 중복을 사용하면 된다. 그걸 지금 구현해본다.

```cpp
#include <iostream>

using namespace std;

/*

포인터 연산자의 중복
pointer operator overloading

*/

class MyIntegerPtr {
private:
	int* p = nullptr;

public:
	MyIntegerPtr(int* _p) : p(_p) {

	}
};

int main(void) {

	while (true) {

		unique_ptr<int> p(new int(40));

	}

	return 0;
}
```

진짜 정보는 p에 저장이 되는데, 껍질을 하나 더 씌워서 포장해준다고 보면 된다.  

```cpp
#include <iostream>

using namespace std;

/*

포인터 연산자의 중복
pointer operator overloading

*/

class MyIntegerPtr {
private:
	int* p = nullptr;

public:
	MyIntegerPtr(int* _p) : p(_p) {

	}
	~MyIntegerPtr() { // 이렇게 하면 스마트 포인터가 된다.
		if (p != nullptr) {
			delete p;
		}
	}
};

int main(void) {

	while (true) {
		MyIntegerPtr p(new int(40));
	}

	return 0;
}
```

여기서 p는 int pointer를 갖고 있는 객체일 뿐이다. 포인터는 객체이다. 객체라는 것은 자신이 생성된 block이 끝나면 반드시 destructor를 호출하므로, 자동으로 해제가 되는 스마트 포인터가 된다.

```cpp
#include <iostream>

using namespace std;

/*

포인터 연산자의 중복
pointer operator overloading

*/

class MyIntegerPtr {
private:
	int* p = nullptr;

public:
	MyIntegerPtr(int* _p) : p(_p) {

	}
	~MyIntegerPtr() { // 이렇게 하면 스마트 포인터가 된다.
		if (p != nullptr) {
			delete p;
		}
	}
};

int main(void) {

	while (true) {
		MyIntegerPtr* p = new MyIntegerPtr(new int(50));
	}

	return 0;
}
```

이와 같이 무한 루프 안에 MyIntegerPtr* p를 만들면, 이 것은 객체가 아니기 때문에 자동으로 해제를 해주지 못해서 memory leak이 발생한다. 

> 즉, 스마트 포인터의 정체는 객체이고, 그 안에 포인터를 가지고 있는 객체일 뿐이다. block이 끝났을 때 자동으로 destructor가 불리게 하기 위해서는 smart pointer를 객체로 구현할 수 밖에 없었다.

```cpp
#include <iostream>

using namespace std;

/*

포인터 연산자의 중복
pointer operator overloading

*/

class MyIntegerPtr {
private:
	int* p = nullptr;

public:
	MyIntegerPtr(int* _p) : p(_p) {

	}
	~MyIntegerPtr() { // 이렇게 하면 스마트 포인터가 된다.
		if (p != nullptr) {
			delete p;
		}
	}
	int& operator*() { // or int로 복사본으로 넘겨줘도 된다.
		return *p;
	}
};

int main(void) {

	while (true) {
		MyIntegerPtr p(new int(40));
		// MyIntegerPtr* p = new MyIntegerPtr(new int(50));

		cout << *p << endl;
	}

	return 0;
}
```

메인을 보면, p는 사실 객체이기 때문에 *p가 불가능하다. 하지만, 이를 구현할 수 있는데, 그건 operator overloading을 사용하면 된다.
