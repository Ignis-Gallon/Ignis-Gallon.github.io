---
title: "6.4 Recap"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, Future, JSON, named constructor]
---

# 6.4 Recap

작성일 2023-03-01 · 최종 수정일 2023-03-04
{ .post-date }

- Future란, 당장 완료될 수 있는 작업이 아니라는 걸 말한다. 그렇기 때문에 우리는 이 작업이 끝날 수 있을 때까지 기다린다. 그 작업이 마무리되면 Response를 저장하며, response.statusCode가 200이라면 성공이므로 받아온 JSON 데이터를 parse한다. 
- final webtoons = jsonDecode(response.body); 이와 같이 json을 Flutter에서 사용가능한 값으로 decode 할 수 있다. 이 때, final List&lt;dynamic&gt; webtoons = jsonDecode(response.body); 와 같이 타입을 명시해줄 수도 있지만, 꼭 필요한 것은 아니다.
- for in 구문을 활용하면 List를 순차적으로 탐색할 수 있다.
- named constructor를 이용하여 API의 값을 받는 형태가 플러터에서 자주 보이므로, 자주 사용할 수 있다.

```dart
class WebtoonModel {
  final String title, thumb, id;

  WebtoonModel.fromJson(Map<String, dynamic> json)
      : title = json['title'],
        thumb = json['thumb'],
        id = json['id'];
}
```

- named constructor를 사용하지 않고 required를 이용해서 값을 받아줄 수도 있다.
- Map은 Object처럼 Dart가 지원하는 key-value 데이터 구조이다.
- fromJson named constructor는 Flutter에서 굉장히 자주 사용되는 패턴이다.
