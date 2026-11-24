---
title: "6.3 fromJson"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, JSON, named constructor]
---

# 6.3 fromJson

- 이제 서버로부터 전달받는 Json 형식을 Dart, Flutter에서 사용할 수 있는 형식인 데이터 클래스로 바꾸어 주어야 한다.

jsonDecode(response.body);를 실행하면, Json을 decode할 수 있다. 이렇게 하면 dynamic Type을 반환받게 되는데, dynamic은 모든 타입을 다 전달받을 수 있다.

- named constructor

```dart
class WebtoonModel {
  final String title, thumb, id;

  WebtoonModel.fromJson(Map<String, dynamic> json)
      : title = json['title'],
        thumb = json['thumb'],
        id = json['id'];
}
```

- title, thumb, id 등을 초기화 할 수 있는 구문

async function 내에서 return을 할 때는 Future를 return해주어야 한다.
