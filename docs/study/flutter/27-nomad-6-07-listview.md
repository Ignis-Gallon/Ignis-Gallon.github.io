---
title: "6.7 ListView"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, ListView, ListView.builder]
---

# 6.7 ListView

작성일 2023-03-01 · 최종 수정일 2023-03-04
{ .post-date }

- 많은 양의 데이터를 한 번에 보여주고 싶을 때 Column이나 Row는 적절하지 않다. 이럴 때는 ListView를 사용하는 것이 좋다.
- 단, ListView는 모든 데이터를 한 번에 로드하므로, 최적화되어 있지 않다. 이 때 사용할 수 있는 것이 바로 ListView.builder이다. ListView.builder는 좀 더 최적화된 ListView이다.
- ListView.builder

현재 코드에서 FutureBuilder로 오늘의 웹툰을 가져오는 API의 실행을 대기해주고 있다. 이 때, builder에서 snapshot.hasData가 true인 경우에만 ListView.builder를 return하는데, ListView.builder는 itemBuilder를 갖게 되는데, 이는 아이템을 build할 때 호출하는 함수이다. 어떤 아이템이 build되는지 알 수 있는 유일한 방법은 바로, itemBuilder가 인자로 받는 index를 이용하는 것이다. 

ListView.builder는 모든 item을 build하는 대신에 만들려는 아이템에 itemBuilder 함수를 실행한다. 그러면 여기서 build 되는 아이템에 index로 접근할 수 있는 것이다.

- ListView.separated : 필수 인자를 하나 더 가진다. separatorBuilder인데, widget을 return해야 하는 함수이다. 그리고 그 widget은 리스트 아이템 사이에 렌더된다.  (SizedBox를 넣어서 구분이 가능해진다. 처음에는 구분자가 없다.)
