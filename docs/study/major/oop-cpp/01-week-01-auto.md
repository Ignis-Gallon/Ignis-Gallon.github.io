---
title: "1주차: auto"
date: 2022-10-11
last_modified_at: 2022-10-11
category: major
tags: [객체기반SW설계, C++, string, auto]
---

# 1주차: auto

작성일 2022-10-11
{ .post-date }

- string

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(void) {

	string s1 = "hello ";
	string s2 = "world!";

	cout << s1 + s2 << endl;
	cout << (s1 == s2) << endl;

	int k = 10;

	cout << s1 << " " << to_string(k) + s2 << endl;
// to_string function을 이용해서 non-string을
// string으로 간편하게 변환 가능하다.
// 또, string은 ==로 간단히 비교가 가능하며
// +로 string끼리 이어붙일 수 있다.

	return 0;
}
```

- bool

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(void) {

	bool b;
	b = true;

	bool b2;
	b2 = false;

	cout << boolalpha;
	cout << b << " " << b2 << endl;

	cout << noboolalpha;
	cout << b << " " << b2 << endl;

	return 0;
}
```

- auto : type을 자동으로 추론해준다.

    type이 별로 중요하지 않은 곳에서는 유용하게 사용 가능

```cpp
#include <iostream>
#include <string>

using namespace std;

int main(void) {

	auto v = 20;
	auto v2 = 13.3;

	for (auto _e : array) {

		// ...

	}

	return 0;
}
```
