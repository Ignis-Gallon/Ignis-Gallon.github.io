---
title: "7주차: nullptr, 동적 할당, Smart Pointer, 복사 생성자"
date: 2022-10-17
last_modified_at: 2022-10-19
category: major
tags: [객체기반SW설계, C++, 동적 할당, Smart Pointer, 복사 생성자]
---

# 7주차: nullptr, 동적 할당, Smart Pointer, 복사 생성자

작성일 2022-10-17 · 최종 수정일 2022-10-19
{ .post-date }

## # `nullptr`

C언어에서는 pointer 초기화는 항상 0으로 했었다.(Null)  
\#define NULL 0, NULL이 정수값 0으로 define 되어있었음.

C++ : nullptr -&gt; null pointer를 확실히 하기 위한 예약어.  
0, NULL : 정수 0을 의미  
nullptr : 이거야 말로, 진정한 null pointer를 의미한다.

장점.  
가독성이 좋아진다.  
가독성이 좋아진다는 것의 의미는, 아래와 같다.

만약, func가 정수형 또는 포인터를 반환하는 overload function이라고 한다면,  
nullptr이라는 예약어를 만들어 정확히 어떤 것이 반환되는지를 명시해 두는 것이 개발 과정에 직관성을 높여준다.

```cpp
auto p = func();

if (p == nullptr) {
	// ----- //
}
```

```cpp
#include <iostream>

int main(void) {

	auto p = func(); // func는 정수형 혹은 pointer를 반환하는 함수

	if (p == nullptr) {
		// ----- //
	}

	int* p = nullptr;
	int a = 10;
	p = &a;

	return 0;
}
```

## # `Dynamic memory allocation`

```cpp
#include <iostream>
#include <stdlib.h>

int main(void) {

	// DMA : ---> dynamic allocation, malloc, free
	// C++ DMA : ---> new--->malloc, delete->free

	// old fashioned C way
	int* a = (int*)malloc(sizeof(int));
	free(a);

	int* b = (int*)malloc(sizeof(int) * 10);
	free(b);

	// C++
	int* a = new int;
	delete a;

	int* b = new int[10];
	delete[] b;

	return 0;
}
```

> 그렇다면 왜 굳이 new, delete를 사용할까?  
>
> new가 생겨난 이유 : 클래스로부터 객체를 생성(메모리 할당받고, 등등)하기 위해서  
>
> malloc으로 new가 하는 일을 못하는 이유 : malloc으로 객체를 위한 메모리는 할당받을 수 있지만, 값을 호출해줄 수 없다. 즉, 생성자(Constructor)를 호출할 수 없다. 따라서 동적으로 클래스에 대한 메모리를 할당받고 초기화해주려면 생성자를 불러와야 하기 때문에 malloc이 아닌 new를 사용해야 한다.  
>
> delete가 하는 일 : free가 하는 메모리 해제를 담당함과 동시에 클래스의 소멸자를 불러와주는 역할을 한다. (Destructor)

```cpp
#include <iostream>
#include <stdlib.h>

class Person {
public:
	int age;
	Person() {
		age = 10;
	}
	Person(int _age) : age(_age) {

	}
};

int main(void) {

	// 객체의 정적생성
	Person p;
	p.age = 20;

	// 객체의 동적생성
	Person* pp = nullptr; 
	// 얘가 아무도 안가리키니까, 일단 객체를 만들어서 넣어준다.

	// 생성자를 사용하는 만들지 않는 new
	pp = new Person;
	// 위에 있는 pp는 아래에서 재정의 하기 전에 delete를 하지 않으므로,
	// 누수 발생. 따라서 반드시 new <-> delete 가 짝을 이루어야 한다.

	// 생성자를 사용하여 만드는 new
	pp = new Person(21);	

	delete pp;

	return 0;
}
```

그렇다면, 그냥 int 하나 같은 변수들을 동적 할당할 때 왜 new를 사용하는가? 그냥 malloc을 사용하면 되지 않는가?

→ 개발자의 입장에서 어떤거는 malloc쓰고 어떤거는 new를 쓰면 헷갈리고 통일성이 부족해지기 때문에, 그냥 구시대의 유물인 malloc을 날려버리고 new를 사용한다.

추가적으로, 클래스의 포인터를 통해 멤버 변수에 접근하려면 -&gt; 를 사용해야 한다.

## # `Smart Pointer`

new-delete pointer로, delete를 안하면 문제가 생긴다.  
Smart Pointer를 사용하면, delete의 수고로부터 자유가 될 수 있다.

```cpp
#include <iostream>

using namespace std;

class Person {
public:
	int age;
	Person() {
		age = 10;
	}
	Person(int _age) : age(_age) {

	}
};

int main(void) {

	unique_ptr<Person> pp1(new Person(21));
	// unique_ptr이라는 class에서 template Person을 담아두는 것.
	// pp1도 이곳에서는 일종의 객체이다.
	// 사실 Smart Pointer는 솔직히 말하면 클래스의 객체고,
	// 그 객체에서 delete를 수행하도록 해주는 역할을 한다.
	// 이를 프로그래밍에서 wrapper라고 한다.

	return 0;
}
```

C++ 에서 진정한 포인터는 new, delete (or free, malloc) 이다. 그러나, Smart Pointer를 사용하면 메모리 누수를 걱정하지 않아도 된다.

---

## # `함수로 객체 전달하기`

객체를 함수로 넘기는 것을 해볼려고 한다. 객체는 함수로 넘어갈 때 default가 call by value로 넘어가므로, 복사된다. 따라서 이를 참조해서 사용하려면 포인터 또는 참조자를 활용해야 한다.

```cpp
#include <iostream>

using namespace std;

// C++에서 객체들은 기본적으로 함수로 넘어갈 때 call by value로 넘어간다.
// 객체의 복사가 구현이 어렵기 때문에, default로 지원해주고 reference를
// 사용자가 직접 세팅하는 것으로 옵션으로 둔다.

class Pizza {
private:
	int radius; // 피자의 반지름
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
};

void doubleSize(Pizza p) {
	p.setRadius(20);
}

int main(void) {

	Pizza p(10);

	p.showRadius(); // 10 출력
	doubleSize(p);
	p.showRadius(); 
	// 10 출력, call by value이다.
	// call by value는 복사를 해주는 것이기 때문에
	// 함수로 객체를 넘겨도 생성자가 불리지 않는다. (그냥 복사만 해주기 때문이다.)

		

	return 0;
}
```

만약, 함수에 객체를 call by reference로 넘겨주려면 두 가지 방법이 있는데, 첫 번째는 우리가 C언어에서부터 사용하던 방식대로 객체의 주소를 넘겨서 포인터로 사용하면 된다. 

```cpp
#include <iostream>

using namespace std;

class Pizza {
private:
	int radius; // 피자의 반지름
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
};

void doubleSize(Pizza* p) {
	p->setRadius(20);
}

int main(void) {

	Pizza p(10);

	p.showRadius();
	doubleSize(&p);
	p.showRadius(); 

	return 0;
}
```

포인터를 사용하는 방식의 문제점은, 함수에 object p가 주소로 전달되기 때문에 argument p를 잘못 조작하게 되면 주변의 값을 잃거나 변경하게 될 수 있기 때문에 위험성이 존재한다.

그래서 C++에서 나온 두 번째 방법이 뭐냐면, 바로 Reference(참조자)를 이용하는 방법이다.

```cpp
#include <iostream>

using namespace std;

class Pizza {
private:
	int radius; // 피자의 반지름
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
};

void doubleSize(Pizza p) {
	p.setRadius(20);
}

void doubleSize(Pizza* p) {
	p->setRadius(20);
}

/* 그런데, C++에서 참조자는 overloading이 되지 못하므로, 함수 명을 바꿔준다.
void doubleSize(Pizza& p) {
	p.setRadius(20);
}
*/

void doubleSize_re(Pizza& p) {
	p.setRadius(20);
}

int main(void) {

	Pizza p(10);

	p.showRadius();
	doubleSize_re(p);
	p.showRadius(); 

	return 0;
}
```

## # `함수가 객체 반환하기`

```cpp
#include <iostream>

using namespace std;

class Pizza {
private:
	int radius;
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
};

Pizza createPizza(int r) {
	Pizza p(r);
	return p;
}

int main(void) {

	Pizza p(10);

	p.showRadius();

	return 0;
}
```

이 교재에서 하고자 하는 얘기는, 위와 같은 방식으로 p를 넘겨주게 되는데 사실 createPizza 안에서 생성된 함수는 이 함수가 종료되면 할당된 메모리가 해제되며 사라진다. 그럼에도 main 함수에서 이를 전달받아서 사용할 수 있는 이유는 우리가 p를 return하면서 복사하기 때문이다.

```cpp
#include <iostream>

using namespace std;

class Pizza {
private:
	int radius;
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
};

Pizza createPizza(int r) {
	Pizza p(r);
	return p;
}

int main(void) {

	// Pizza p1 = createPizza(10);
	// 함수 안에서 객체를 생성해서 복사를 떠온다.

	// main에서 Pizza class를 만들고, 함수 createPizza 내에서 한번 더
	// 생성해서 복사해주므로, 생성자가 두 번 불리게 된다.
	Pizza p1;
	p1 = createPizza(10);

	return 0;
}
```

이 내용을 자세히 보려면, destructor를 만들어서 자세히 찍어보면 된다.

```cpp
#include <iostream>

using namespace std;

class Pizza {
private:
	int radius;
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
	~Pizza() {
		cout << "Destructor 호출됨" << endl;
	}
};

Pizza createPizza(int r) {
	Pizza p(r);
	return p;
}

int main(void) {

	// Pizza p1 = createPizza(10);
	// 함수 안에서 객체를 생성해서 복사를 떠온다.

	// main에서 Pizza class를 만들고, 함수 createPizza 내에서 한번 더
	// 생성해서 복사해주므로, 생성자가 두 번 불리게 된다.
	Pizza p1;
	p1 = createPizza(10);

	return 0;
}
```

함수가 객체를 넘길 때는 복사본을 만들어서 넘겨준다.  
즉, p의 복사본을 만들어서 넘겨주는데, 복사본은 생성자를 안 부르고 만들어지는 것인가?

- 복사 생성자, copy constructor  
    → 복사할 때는 일반적인 constructor를 사용하는 것이 아니라, 복사를 위해 존재하는 생성자인 copy constructor를 사용한다.

```cpp
#include <iostream>

using namespace std;

class Pizza {
private:
	int radius;
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
Pizza(const Pizza& p) {
	cout << "이게 바로 복사 생성자입니다." << endl;
}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
	~Pizza() {
		cout << "Destructor 호출됨" << endl;
	}
};

Pizza createPizza(int r) {
	Pizza p(r);
	return p;
}

int main(void) {

	Pizza p0 = createPizza(10); // 복사 생성자가 호출된다.

	Pizza p1;
	p1 = createPizza(10);

	return 0;
}
```

만약 복사 생성자를 custom으로 만들지 않는다면, 기본 복사 생성자가 생성된다. 지금까지 복사 생성자를 사용한 두 곳은 첫 번째로 함수에 argument(call by value)로 전달해줄 때, 두 번째는 함수에서 본인이 사라지기 전에 지역 변수로 만들어진 객체를 main으로 복사해서 반환해줄 때.

그리고 마지막은, 다른 객체를 복사해서 다른 객체로 만들 때이다. 마지막 이유가 왜 복사 생성자가 필요한지, 왜 custom 복사 생성자를 만들어야 되는지에 대한 Highlight라고 할 수 있다.

```cpp
#include <iostream>

using namespace std;

class MyArray {
public:
	int size;
	int* data;
	MyArray(int _size) {
		this->size = _size;
		data = new int[this->size]; // 정수 배열을 선언한다.
	}
	~MyArray() { // 내가 가져온 쓰레기(동적 할당 배열)는 내가 가져 간다. 
	// -> 싫으면 unique pointer
		delete[] data;
	}
};

int main() {

	MyArray a(10);
	a.data[5] = 123456;

	cout << "value : " << a.data[5] << endl;

	{
		MyArray b = a;
	}

	cout << "value : " << a.data[5] << endl;

	return 0;
}
```

a.data[5]의 값이 바뀌는 것을 확인할 수 있다. 왜 그럴까? 왜냐면, b는 a의 복사본이므로 int* data는 서로 같은 원본을 참조하고 있다. 그런데, { } 블록이 종료되면 b는 사라져야 하므로, 객체 b는 소멸되면서 Destructor를 호출하고, 이렇게 되면 data가 사라지게 된다. (메모리가 해제됨) 이런 문제를 막기 위해서, 문제를 해결하고자 복사 생성자를 custom해서 동적 할당 받는 부분은 새로 만들어지도록 한다. 사실 복사 생성자를 만들지 않으면 복사는 알아서 해준다. 그러나 custom copy constructor를 만드는 순간 c++은 객체의 복사에 대해서 손을 놓게 된다. 이렇게 되면 프로그래머가 스스로 모든 필드에 대한 복사를 선언해주어야 한다.

```cpp
#include <iostream>

using namespace std;

class MyArray {
public:
	int size;
	int* data;
	MyArray(int _size) {
		this->size = _size;
		data = new int[this->size]; // 정수 배열을 선언한다.
	}
	MyArray(const MyArray& p) {
		this->size = p.size;
		this->data = new int[this->size];

		for (int i = 0; i < p.size; i++) {
			this->data[i] = p.data[i];
		}
	}
	~MyArray() { // 내가 가져온 쓰레기(동적 할당 배열)는 내가 가져 간다. 
	// -> 싫으면 unique pointer
		delete[] data;
	}
};

int main() {

	MyArray a(10);
	a.data[5] = 123456;

	cout << "value : " << a.data[5] << endl;

	{
		MyArray b = a;
	}

	cout << "value : " << a.data[5] << endl;

	return 0;
}
```

> Shallow Copy : 이전에 했던 동작. 아주 단순하게 값을 읽어오는 것.  
> (this-&gt;data = p.data;), 메모리를 적게 먹는다.  
>
> Deep Copy : 위에서 했던 대로 새롭게 공간을 할당 받고 값을 복사해주는 것처럼 제대로 깊게 복사해 주는 것, Deep copy를 많이 쓰면 메모리가 감당이 안될 때도 있다.

## # `복사 생성자와 대입 연산자, 클래스 안에 객체 포함`

```cpp
#include <iostream>

using namespace std;

class Pizza {
private:
	int radius;
public:
	Pizza(int r = 0) : radius(r) {
		cout << "Pizza constructor was invoked!" << endl;
	}
	Pizza(const Pizza& p) {
		cout << "이게 바로 복사 생성자입니다." << endl;
	}
	void setRadius(int r) {
		this->radius = r;
	}
	void showRadius() {
		cout << "pizza radius: " << this->radius << endl;
	}
	~Pizza() {
		cout << "Destructor 호출됨" << endl;
	}
};

Pizza createPizza(int r) {
	Pizza p(r);
	return p;
}

void doubleSize(Pizza _p) {
	_p.setRadius(20);
}

int main(void) {

	Pizza p1(10);
	Pizza p2(20);
	p2 = p1; 
	// 이런 경우에는 그냥 대입 연산자가 활용된다.
	// 복사 생성자가 호출되지 않는다.

	Pizza p3 = p1;
	// p3는 복사가 되는 것이므로, p1을 인수로 전달받아서
	// 복사 생성자를 호출한다.

	Pizza p4(p1);
	// p3와 마찬가지로 p1이 복사가 되는 것이다.
	// 단지 방법이 다를 뿐이다.

	// p2 = p1은 서로 집이 있는 객체들이므로, 그냥 대입 연산자를 활용해
	// 값을 대입해준다. 복사 생성자를 호출하는 것이 아니다.

	// 이와 다르게 p3, p4는 p1을 인수로 전달받아 복사 생성자를 호출하는 것이다.

	return 0;
}
```

```cpp
#include <iostream>

using namespace std;

class A {
	int a;
};

class B {
	float d;
};

class D {
	B* b;
	A k;
};

int main(void) {

	

	return 0;
}
```

Class를 생성하는 순간, Class는 그 자체로 하나의 타입이 되므로 다른 Class 내의 멤버 변수로서 들어갈 수 있다.

## # `정적 변수`

Class에서 멤버 변수를 만들 때, 멤버 변수 앞에 static을 붙여 정적 변수를 만들어 줄 수 있다. static으로 멤버 변수를 선언하면, 해당 Class의 instance들은 모두 동일한 하나의 변수를 공유하게 된다. (only 1 copy)

이 것이 왜 필요한가? 객체들이 동일하게 하나의 변수를 가져야 하는 경우가 발생한다. 예를 들어 Class 내에서 어떤 작업을 하고 싶을 때 순서대로 Queue에 넣어서 작업 하고 싶다. 그러면 static으로 Class 내에 Queue를 선언해서 job을 넣어줘서 처리해주면 된다.

Static 변수는 Class내에 별개로 들어있기 때문에 Class의 instance를 생성하지 않고도 사용할 수 있다.

```cpp
#include <iostream>

using namespace std;

class Lemon {
public:
	int x, y;
	static int count;
};

int main(void) {

	Lemon l1;
	l1.x = 20;

	Lemon l2;
	l2.x = 40;

	l1.count = 40;
	l2.count = 50;

	cout << Lemon::count << endl;
	cout << l1.x << " " << l2.x << endl;

	return 0;
}
```
