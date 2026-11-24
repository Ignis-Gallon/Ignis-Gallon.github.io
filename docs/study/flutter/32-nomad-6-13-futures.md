---
title: "6.13 Futures"
date: 2023-03-03
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, Future, StatefulWidget, late]
---

# 6.13 Futures

작성일 2023-03-03 · 최종 수정일 2023-03-04
{ .post-date }

- 앞서 만든 Data class와 Api service를 Detail Screen에서 이용한다.

- 이전에는 HomeScreen에 Future를 가져오는 Service를 호출했었다. 그리고 FutureBuilder를 이용해서 Future로부터 값을 받아오는 걸 기다렸었다. DetailScreen에 이 방식을 그대로 사용할 수는 없는데, 왜냐면 getLatestEpisode와 getToonById에는 인자로 id를 전달하기 때문이다. 그대로 구현하기에는 약간의 문제가 있다.

    ```dart
    Future<WebtoonDetailModel> webtoon = ApiService.getToonById(id);
    ```

    이와 같이 코드를 쓰면 id에 접근할 수 없다고 나오는데, 그 이유는 클래스의 메서드 외부에서는 property를 define하고 initialize하는 것 밖에 사용할 수 없기 때문이다.

- 그래서 어떤 property를 초기화할 때 다른 property에 접근할 수는 없다.
- 이를 해결하려면 DetailScreen을 StatelessWidget에서 StatefulWidget으로 변경해야 한다.
- code action을 이용하여 DetailScreen을 StatefulWidget으로 바꾸게 되면, property를 사용하던 기존의 코드들의 앞에 widget.이 붙게 된다. 즉, widget.title, widget.id 와 같은 방식으로 바뀌게 되는 것이다.
- 그 이유는, StatefulWidget으로 바꾸게 되면 별개의 두 개의 클래스가 만들어지게 되는데, 하나는 property를 정의하고 State Class를 호출해주는 역할을 하며, 다른 하나는 UI를 렌더링하는 역할(호출 당함)을 한다. 이 때, property는 property를 정의하는 클래스에서 만들어지는 것이므로, State class에서 알 수 없기 때문에 widget. 을 붙여준다.
- 우리는 Future를 State에서 사용하고 싶기 때문에, 선언도 State에서 해주어야 한다. 그러려면 widget.id를 이용하여 API의 인자로 넘겨주어야 한다.
- 하지만 이렇게 해도 여전히 오류가 발생하는데, 그 이유는 constructor에서 widget을 참조할 수 없기 때문이다. 그러므로, initState를 사용하여 StatefulWidget이 처음 렌더링될 때 값을 가져올 수 있도록 하자.
- 그리고 Future는 나중에 초기화될 수 있도록 late 키워드를 지정해주자.

(이와 같이 초기화하고 싶은 property가 있지만, constructor에서는 불가능한 경우 late 키워드를 사용하여 나중에 초기화시킬 수 있다. initState가 항상 build보다 먼저 호출되며, 단 한번만 호출되기 때문에 초기화에 적합한 메서드이다.)
