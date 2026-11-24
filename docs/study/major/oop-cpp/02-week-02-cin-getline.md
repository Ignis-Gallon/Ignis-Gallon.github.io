---
title: "2주차: cin과 getline"
date: 2022-10-11
last_modified_at: 2022-10-11
category: major
tags: [객체기반SW설계, C++, cin, getline]
---

# 2주차: cin과 getline

작성일 2022-10-11
{ .post-date }

- cin과 getline

    cin은 공백을 \\n(Enter, Carriage return)으로 인식한다.  
    따라서, 긴 문장을 입력하려면 다른 방법을 써야한다.

> cin &gt;&gt; str;  
> getline(cin, str);

getline(cin, str)은 carriage return이 아니면, 공백도 문자로 취급한다.

만약, cin을 이용해서 입력받으면, Enter로 인해 buffer에 carriage return(\\n)이 들어가 있기 때문에, cin을 사용한 뒤에 getline을 사용할 것이라면 cin.ignore();를 통해 버퍼에 들어있는 문자 (\\n)를 하나 비워줘야 한다.

```cpp
string names[] = {"tom", "jane", "jerry" };

for (auto& v : names) {
	cout << v << endl;
}

// range based for loop
// 참조자를 이용하면 배열 names를 복사해서 가져오지 않고
// 직접 참조하기 때문에, 메모리와 속도 면에서 유리하다.
```
