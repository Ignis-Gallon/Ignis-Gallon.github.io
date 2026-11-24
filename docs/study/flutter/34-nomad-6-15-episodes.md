---
title: "6.15 Episodes"
date: 2023-03-03
last_modified_at: 2023-03-12
category: study
tags: [Flutter, 노마드코더, FutureBuilder, collection for]
---

# 6.15 Episodes

- 이전에 DetailScreen을 StatefulWidget으로 변환한 유일한 이유는 initState 메서드가 필요하기 때문이었다. 그래야 getToonById랑 getLatestEpisodesById를 사용할 수 있기 때문이다.
- 이전의 HomeScreen은 getTodaysToons가 어떤 데이터도 필요로 하지 않기 때문에 이와 같은 과정이 필요하지 않았지만, DetailScreen에서 사용되는 API는 의존성을 갖기 때문에 이와 같은 과정이 필요했다.
- 회차를 표시하기 위해 Column을 사용한다. 만약, 내가 가져올 데이터의 길이를 모른다면 ListView를 사용하는 것이 맞다. 하지만 이번에는 고정적으로 10개만을 가져올 것이기 때문에 Column을 사용하는 것이 유리하다.

```dart
FutureBuilder(
	future: episodes,
	builder: (context, snapshot) {

  if (snapshot.hasData) {
	  return Column(
	    children: [
	      for (var episode in snapshot.data!) Text(episode.title)
     ],
	  );
  }

	  return Container();
  },
)
```

- 이 코드에서 Collection for를 사용할 때, { } 를 열고 Text를 사용하면 오류 발생. 아무래도 저 방식으로만 사용할 수 있는듯.
