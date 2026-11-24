---
title: "Set Map"
date: 2022-11-30
last_modified_at: 2022-12-06
category: major
tags: [객체기반SW설계, C++, STL, set, map]
---

# Set Map

- Map은 define으로 #define Pair pair&lt;string, int&gt; 와 같이 선언해주고, Pair로 데이터를 전달받으면 algorithm header에 들어있는 함수 사용 가능할 것임…

map의 정렬 방법? ⇒ pair로 vector 선언, vector에 map의 원소들 넣어주고 sort.

- map과 set은 iterator 반환 가능하지만, sort나 find, count, remove 등등 사용하기 힘듦.

```cpp
#define myPair pair<string, int>

  cout << "Map" << endl;
	// map과 set은 순서가 없다.
	map<string, int> my_map;

	my_map["apple"] = 10;
	my_map["banana"] = 20;
	my_map["arape"] = 30;
	my_map["biwi"] = 40;
	my_map["aelon"] = 50;

	vector<myPair> myVect;
	myVect.resize(my_map.size());
	cout << "Check!\n";
	for (auto i = my_map.begin(); i != my_map.end(); i++) {
		myPair mp;
		mp.first = (*i).first;
		mp.second = (*i).second;
		
		myVect.push_back(mp);
	}

	sort(myVect.begin(), myVect.end(), [](myPair& _a, myPair _b) {
		return(_a.second > _b.second);
	});

	for (auto& _e : myVect) {
		cout << _e.first << " " << _e.second << endl;
	}

	// my_map.erase(my_map.begin());
	for (auto& _e : my_map) {
		cout << _e.first << "의 가격은 : " << _e.second << endl;
	}

	// map은 algorithm STL 사용이 힘들다. key : value 쌍이어서...
	// 그냥 iterator 돌면서 custom으로 만드는게 나을듯.
	cout << endl;
	for (auto i = my_map.begin(); i != my_map.end(); i++) {
		
		if ( ((*i).first)[0] < 'c') {
			cout << (*i).first << " " << (*i).second << endl;
		}
	}
	for (auto& _e : my_map) {
		cout << _e.first << "의 가격은 : " << _e.second << endl;
	}
	// reverse 불가.
	/*reverse(my_map.begin(), my_map.end());
	for (auto& _e : my_map) {
		cout << _e.first << "의 가격은 : " << _e.second << endl;
	}*/
	// 만약, 역순으로 탐색하고 싶다면, iterator를 거꾸로 만들어서 하면 됨.

	// auto it = my_map.find("apple");
	// if (auto it != my_map.end()) my_map.erase(it);

	// my_map.count("apple");

	cout << "\n\n\n";
	cout << "Set" << endl;

	set<int> my_set; // set은 insert이다.
	my_set.insert(1);
	my_set.insert(1);
	my_set.insert(2);
	my_set.insert(2);
	my_set.insert(3);
	my_set.insert(3);

	for (auto& _e : my_set) {
		cout << _e << endl;
	}

	my_set.erase(my_set.find(1));
	cout << my_set.count(2) << endl;

	// map과 set은 이전의 container들에 적용되던
	// algorithm 헤더에 있는 count, remove, find, reverse, sort 적용 불가능. for_each는 가능할 것(map은 불가)
	// 그냥, range based for loop돌면 됨...

	cout << endl;
	for_each(my_set.begin(), my_set.end(), [](int _x) {
		cout << _x << endl;
	});
```
