---
title: "Vector List"
date: 2022-11-30
last_modified_at: 2022-12-01
category: major
tags: [객체기반SW설계, C++, STL, vector, list]
---

# Vector List

작성일 2022-11-30 · 최종 수정일 2022-12-01
{ .post-date }

- Vector : array기반의 STL. 사용할 수 있는 메서드는 다음과 같다.

선언 : vector&lt;T&gt; name;

`vector.push_back()` : 뒤에 원소를 넣는다.

`vector.pop_back()` : 맨 뒤에서 원소를 뺀다. 단, 반환하지 않음.

`vector.front(), vector.back()` : 맨 앞과 맨 뒤의 원소 값을 반환한다. 제거하지는 않음.

`vector.begin(), vector.end()` : iterator 반환 가능. array 기반이기 때문에 주소 연산 가능.  
→ 주소 연산이 가능하다는 것은, vector.erase()와 같은 것을 사용할 때 주소 연산을 사용해서 범위를 지정할 수 있다는 뜻이다.

`vector.rbegin(), vector.rend()` : iterator 반환 가능하다.   
→ rbegin은 end와 동일, rend는 begin과 동일.

- 역순의 iterator로 출력하려면 이렇게 출력해야함. i--가 아님. i++임.

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main(void) {

	vector<int> v = { 1, 2, 3, 4, 5 };
	
	for (auto i = v.rbegin(); i != v.rend(); i++) {
		cout << (*i) << " ";
	}

	return 0;
}
```

반복문에서는 rbegin, rend 또는 begin, end를 짝으로 사용해야 한다. 추가로, 위의 예제처럼 iterator를 반환했을 때, iterator는 주소이므로 (*i)로 값을 반환받을 수 있음.

`vector[idx], vector.at(idx)` : 인덱싱을해서 값을 가져올 수 있음.

`vector.clear()` : vector를 비운다.

`vector.size()` : vector의 사이즈를 반환한다.

`vector.resize()` : vector의 사이즈를 바꾼다.  
→ size를 바꾸면 뒤에서 부터 원소가 잘리고, 기존 사이즈보다 크게 만들면 안에는 0으로 채워짐.

`vector.empty()` : 비어있는지 확인한다. bool 반환.

`vector.insert(iterator, data)` : iterator 위치의 ‘앞’에 데이터를 넣는다. 

`vector.reverse()` : 벡터의 순서를 뒤집는다.

`vector.erase()` : 벡터에서 원소를 제거한다. iterator 기반으로 진행.

```cpp
// 단일 원소 지우기
vector.erase(vector.begin());
vector.erase(vector.end());
vector.erase(vector.begin()+5);

// 연속적으로 원소 지우기, last 이전의 원소까지만 지우게 된다.
vector.erase(vector.begin(), vector.begin()+5);
// 이렇게 하면, vector.begin()+5 이전의 원소까지 지운다.
```

> &lt;algorithm&gt;에서 find 혹은 find_if를 사용해 원소를 찾아서 제거할 수 있음.

```cpp
auto it1 = find(vector.begin(), vector.end(), 10);
auto it2 = find_if(vector.begin(), vector.end(), [](int x) {
	return (x % 2 == 0);
});

// 이처럼 반드시 vector.end() 처리를 해주어야 함.
if (it2 != vector.end()) {
	vector.erase(it2);
}
```

---

- list : 리스트는 DLL이다. 따라서, access by indexing이 불가능하다.

선언 : list&lt;T&gt; name;

`list.push_front(), list.push_back()` : 데이터를 추가한다.

`list.pop_front(), list.pop_back()` : 맨 앞과 맨 뒤에서 데이터를 제거한다. 값을 반환하지 않는다.

`list.front(), list.back()` : 맨 앞과 맨 뒤에 있는 원소의 값을 반환한다. 값을 제거하지 않는다.

`list.clear()` : 리스트를 비운다.

`list.begin(), list.end()` : iterator 반환.

`list.rbegin(), list.rend()` : iterator 반환. 역순.

```cpp
for (auto i = l.begin(); i != l.end(); i++) {
		cout << *i << endl;
	}

	for (auto i = l.rbegin(); i != l.rend(); i++) {
		cout << *i << endl;
	}
```

`list.empty()` : 비어있는지 체크

`list.size()` : size 반환

`list.insert(iterator, data)` : iterator ‘앞’에 데이터 추가

`list.resize()` : list를 resizing한다. 크기를 줄이면 뒤에서부터 자르고, 크기가 커지면 0으로 채운다.

`list.erase()` : list에서 원소를 제거한다. iterator 기반으로 진행.

```cpp
// 단일 원소 지우기
list.erase(iterator)

// 연속적으로 지우기
list.erase(list.begin(), list.end());
list.erase(list.begin(), list.begin()+5);
// list는 array 기반이 아닌 DLL이기 때문에, 위의 예시처럼 주소 연산은 불가능.
// find를 쓰거나, remove를 사용해야 할 것으로 보임.
```

`list.reverse()` : list를 뒤집는다.

`l.sort()` : vector를 sort한다. 함수를 전달해주면, 정렬 순서를 정할 수 있다. (람다 사용 가능)

```cpp
list.sort([](int& x, int& y) {
		return (x > y);
	});
```

list.remove(data), list.remove_if() : list에서 값을 찾아 제거할 수 있다. 범위 지정이 필요하지 않음.

```cpp
l.remove(10);

l.remove_if([](int x) {
	return (x % 2 != 0);
});
```

---

> 두 함수 모두, range based for loop을 사용할 수 있다. range based for loop은 iterator를 뽑을 수 없는 자료구조에서만 사용할 수 없다. 동적할당받은 배열은 iterator를 추출할 수 없기 때문에, range based for loop이 안된다. list가 vector와 다른 점은 array based가 아니기 때문에 주소 연산이 안된다는 것이다.
