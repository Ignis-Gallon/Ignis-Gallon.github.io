---
title: "6.6 FutureBuilder"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, FutureBuilder, snapshot]
---

# 6.6 FutureBuilder

작성일 2023-03-01 · 최종 수정일 2023-03-04
{ .post-date }

- 데이터를 fetch해서 State에 넣는 방법이 있다.

하지만, State는 최대한 사용하지 않는 것이 좋다. 왜냐면 State는 그렇게 많이 사용되지 않으며 State를 전혀 안쓰는 좋은 Widget과 Framework가 많이 있다. 그러므로, State 파트를 모두 제거한다. 그리고 사실 ApiService는 사용하지 않을 수 있다.

- 데이터를 fetch하는 다른 방법 (StatelessWidget인 상태에서 fetch 가능)

플러터에서 const는 컴파일 타임 이전에 그 값을 알고 있어야 적용 가능하다. 그러므로, Future를 사용하면 const는 당연히 사용할 수 없게 된다. Future는 API로부터 오는 값이므로  const 안에서 사용할 수 없다.

- FutureBuilder : builder라는 매개변수가 필요하다. Builder는 UI를 그려주는 함수이며, initialData도 전달할 수 있고, Future도 전달할 수 있다.

    이번 예제에서는, FutureBuilder에게 Future를 기다려달라고 지정해보자. FutureBuilder에게 Future를 넘겨주면 자동으로 await 처리를 해주는 것이다.

- FutureBuilder에서 builder는 두 개의 인자를 받는다. 하나는 context(Widget Tree에서 상위 Widget에 접근할 수 있도록 하는 요소)이고, 다른 하나는 snapshot이다.
- snapshot : 스냅샷을 이용하면 future의 상태를 알 수 있다. future가 데이터를 받았는지, 아니면 오류가 발생했는지를 알 수 있다.

FutureBuilder를 이용하면 StatefulWidget을 쓰지 않더라도 fetch를 처리할 수 있다.
