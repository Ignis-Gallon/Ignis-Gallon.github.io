---
title: "12주차 (2): set, map, container adapter"
date: 2022-11-23
last_modified_at: 2022-11-28
category: major
tags: [객체기반SW설계, C++, STL, set, map]
---

# 12주차 (2): set, map, container adapter

작성일 2022-11-23 · 최종 수정일 2022-11-28
{ .post-date }

- set(순서가 없으며 중복을 허용하지 않는다.)

```cpp
#include <iostream>
#include <set>

using namespace std;

int main(void) {

	set<int> my_set;
	
	my_set.insert(1);
	my_set.insert(2);
	my_set.insert(3);

	// iterator, 종료 조건은 i != my_set.end()이다.
	for (auto i = my_set.begin(); i != my_set.end(); i++) {
		cout << *i << endl;
	}

	cout << endl;

	// range based for loop
	for (auto& _e : my_set) {
		cout << _e << endl;
	}

	return 0;
}
```

- map(key : value, hash table)

```cpp
#include <iostream>
#include <map>

using namespace std;

int main(void) {

	// map : hash table => key : value로 이루어짐.
	map<string, int> my_map;

	my_map["john"] = 20;
	my_map["tom"] = 19;

	// string을 숫자로 변환하고 hash function에 집어넣어서 계산한 뒤
	// index를 얻어서 해당 위치로 찾아간다.
	cout << "john age: " << my_map["john"] << endl;
	cout << "tom age: " << my_map["tom"] << endl;

	return 0;
}
```

→ 만약 데이터를 저장해야 하는데, index로 표현하기 힘들고 index대신 key를 string으로 저장하는 것이 편하다고 한다면 map(hash table)을 사용하면 된다.

---

- container adapter

이전에 stack을 구현할 때는 Array를 이용해 스택을 구현했는데, C++의 STL에서 제공하는 스택은 우리가 기존에 구현했던 방식과는 차이가 있다. 바로, list와 vector를 stack이라는 wrapper로 싸준다. 이렇게 하면 앞으로 list, vector에 들어가고 나오는 데이터는 stack과 같이 한 구멍으로만 드나들 수 있게 된다. 이를 adapter라고 한다.

이와 마찬가지로 queue, priority queue도 adapter를 제공한다. 그렇다면, 그 안에 들어있는 것은 list 또는 vector인가? … stack과 queue, priority queue는 container가 아니고, 내부적으로는 list, vector를 이용하는 adapter이다.

- stack

```cpp
#include <iostream>

// stack, queue, priority queue는 container가 아니고
// adapter이다.
#include <stack>
#include <queue> // priority queue 포함

using namespace std;

int main(void) {

	stack<int> my_stack;

	my_stack.push(10);
	my_stack.push(20);
	my_stack.push(30);
	cout << my_stack.top() << endl; // 맨 위에 있는 값을 보여준다.

	while (!my_stack.empty()) {
		cout << my_stack.top() << endl;
		my_stack.pop(); // pop은 값을 반환하지 않는다.
	}

	return 0;
}
```

- 기본적으로 stack은 vector를 이용하여 구현한다. 이는 dynamic array이다. 만약 vector가 아니라 list로 만든 stack을 넣고 싶다면, 아래와 같이 해주면 된다.

```cpp
#include <iostream>

// stack, queue, priority queue는 container가 아니고
// adapter이다.
#include <stack>
#include <queue> // priority queue 포함
#include <list>

using namespace std;

int main(void) {

	stack<int, list<int>> my_stack;

	my_stack.push(10);
	my_stack.push(20);
	my_stack.push(30);
	cout << my_stack.top() << endl; // 맨 위에 있는 값을 보여준다.

	while (!my_stack.empty()) {
		cout << my_stack.top() << endl;
		my_stack.pop(); // pop은 값을 반환하지 않는다.
	}

	return 0;
}
```

- queue

```cpp
#include <iostream>

// stack, queue, priority queue는 container가 아니고
// adapter이다.
#include <stack>
#include <queue> // priority queue 포함
#include <list>

using namespace std;

int main(void) {

	// double_ended queue
	// queue<int, deque<int>> my_queue;
	queue<int> my_q;
	my_q.push(10);
	my_q.push(20);
	my_q.push(20);

	cout << my_q.size() << endl;
	cout << my_q.front() << endl;
	cout << my_q.back() << endl;
	cout << "\n\n\n";

	while (my_q.empty() == false) {
		cout << my_q.front() << endl;
		my_q.pop();
	}

	return 0;
}
```

- priority queue

```cpp
#include <iostream>

// stack, queue, priority queue는 container가 아니고
// adapter이다.
#include <stack>
#include <queue> // priority queue 포함
#include <list>

using namespace std;

int main(void) {

	// 우선순위 큐를 만들어서 int를 저장하는데
	// container를 vector로 하겠다.
	priority_queue<int, vector<int>> p_que;

	p_que.push(10);
	p_que.push(20);
	p_que.push(30);

	while (p_que.empty() == false) {
		cout << p_que.top() << endl;
		p_que.pop();
	}

	return 0;
}
```

- C++에서 사용하는 priority queue는 template이 int일 경우에는 숫자가 클 수록 우선순위가 높다고 default로 판단한다. 그런데 만약 숫자가 작을 수록 우선 순위가 높다고 판단하려면, 따로 Compare 클래스를 만들어주어야 한다.

```cpp
#include <iostream>

// stack, queue, priority queue는 container가 아니고
// adapter이다.
#include <stack>
#include <queue> // priority queue 포함
#include <list>

using namespace std;

template<typename T>
class MyCompare {
public:
	bool operator()(T a, T b) {
		if (a < b) {
			return true;
		}
		return false;
	}
};

int main(void) {

	// 우선순위 큐를 만들어서 int를 저장하는데
	// container를 vector로 하겠다.
	priority_queue<int, vector<int>, MyCompare<int>> p_que;

	p_que.push(10);
	p_que.push(20);
	p_que.push(30);

	while (p_que.empty() == false) {
		cout << p_que.top() << endl;
		p_que.pop();
	}

	return 0;
}
```

---

- algorithm

find

count : count_if 등, 조건을 만족하는 것의 개수 세기 가능  
→ count_if, find_if, remove_if …

copy : 복사하기

reverse : 뒤집기

sort : 정렬하기, 기본은 오름차순

for_each : 각 요소에 함수 적용

- find, find_if, sort

```cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

// condition이 다 다를 수 있기 때문에
// 오버로딩으로 지정 불가능.
bool myCondition(string s) {

	if (s.length() >= 6) {
		return true;
	}
	else {
		return false;
	}
}

bool my_str_sort(string& _a, string& _b) {

	// string은 대소 비교가 가능하다.
	if (_a > _b) {
		return true;
	}
	else {
		return false;
	}
}

int main(void) {

	vector<string> myf = { "사과", "바나나", "토마토", "배" };
	
	// 토마토가 안에 들어있는지 확인해보자. find() 사용.
	// 이 패턴만 알면, 나머지는 다 비슷한 패턴이다.
	// 검색할 범위를 지정한다. 범위는 iterator로 지정.
	// [ A , B ) ==> A는 포함, B는 포함되지 않음.
	// return되는 값은 iterator이다. 만약, container안에
	// 값이 들어있지 않다면 myf.end()를 return한다.
	auto it = find(myf.begin(), myf.end(), "토마토");
	if (it == myf.end()) {
		cout << "찾을 수 없습니다.\n";
	}
	else {
		cout << "찾았습니다... " << *it << endl;
	}

	cout << "\n\n\n";

	auto it2 = find_if(myf.begin(), myf.end(), myCondition);

	if (it2 == myf.end()) {
		cout << "찾을 수 없습니다." << endl;
	}
	else {
		cout << "찾았습니다. : " << *it2 << endl; // 바나나
	}

	cout << "\n\n\n";

	sort(myf.begin(), myf.end());

	for (auto& _e : myf) {
		cout << _e << endl;
	}

	cout << "\n\n\n";

	sort(myf.begin(), myf.end(), my_str_sort);

	for (auto& _e : myf) {
		cout << _e << endl;
	}

	return 0;
}
```

> 우리가 만든 클래스를 정렬하고 싶다면 custom compare 함수를 만들어서 반드시 sort() 함수에 iterator와 함께 넣어주어야 한다.

- for_each

```cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

void my_func(string& _s) {
	cout << "문자열 : " << _s << endl;
}

int main(void) {

	vector<string> myf = { "사과", "바나나", "토마토", "배" };

	for_each(myf.begin(), myf.end(), my_func);

	cout << "\n\n\n";

	reverse(myf.begin(), myf.end());

	for_each(myf.begin(), myf.end(), my_func);

	return 0;
}
```

---

- Lambda function == Lambda expression

함수는 기본적으로 이름을 가지지만, 이름이 없는 함수를 람다 함수라고 한다. 한 번 쓰고 버리는 함수한테 굳이 이름을 붙여줄 필요가 없기 때문에, 그냥 쓰고 버리는 것이다.

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

bool my_my_func(int& _i) {
	if (_i % 2 == 0) {
		return true;
	}
	else {
		return false;
	}
}

int main(void) {

	vector<int> numbers = { 1, 2, 3, 4, 5 };

	auto count = count_if(numbers.begin(), numbers.end(), my_my_func);

	cout << "일반 함수 개수 세기 : " << count << "\n\n\n";

	auto count2 = count_if(numbers.begin(), 
		numbers.end(),
		[](int x) { if (x % 2 == 0) { return true; } else { return false; } }
	);

	cout << "람다 함수 개수 세기 : " << count2 << endl;

	return 0;
}
```

람다 함수는 너무 복잡한 것으로 짜면 안된다. return type의 명시는 가능하지만, 굳이 명시까지 해줘야되는 걸 람다로 만드는 것은 좋지 않다.
