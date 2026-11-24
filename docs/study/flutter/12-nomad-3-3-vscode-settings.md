---
title: "3.3 VSCode Settings"
date: 2023-02-28
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, VSCode, const]
---

# 3.3 VSCode Settings

작성일 2023-02-28 · 최종 수정일 2023-03-04
{ .post-date }

- 파란 줄이 나오는 이유 : const 사용을 추천한다. Dart는 constant 개념을 사용하는데, 이는 value를 사전에 알고 있는 것이다. 
- 변수를 constant로 선언하게 되면, 변수가 사용하는 부분이 compile 과정에서 메모리를 할당하는 것이 아니라 지정되어 있는 값을 가져오는 최적화를 실행한다. (런타임에서 값이 정해지는 것이 아니라 컴파일 과정에서 이미 값이 정해져 있는 것)
- 예를 들어 배경색의 경우는 바뀌지 않을 것이기 때문에 const로 선언하는 것이 더 좋을 수도 있다. 런타임 대신, 컴파일러가 컴파일 하는 중에 값을 산정할 수 있는 것이다. 
- 어떤 것은 const가 될 수 있고, 어떤 것은 const가 될 수 없는 것을 기억하는 것은 매우 어렵다. 그러므로 VScode의 기능을 사용하자.

Ctrl + P ⇒ settings.json ⇒ 아래 내용 입력

```json
"editor.codeActionsOnSave": {
	"source.fixAll": true
}
```

이렇게 만들고 저장하면, 자동으로 코드가 수정되면서 파란 줄이 사라진다. (const가 되어야 할 곳은 const가 된다.) 

```json
"dart.previewFlutterUiGuides": true,
```

이 설정도 입력해준다. 이렇게 하면 부모-자식 관계를 보여주는 guide line이 생성된다.

- withOpacity 옵션의 경우, 그 값을 const로 사용할 수 없다. 그러므로 const로 선언하면 안된다.
- crossAxisAlignment : 수직축(세로)을 기준으로 정렬한다. 
- mainAxisAlignment : 수평축(가로)을 기준으로 정렬한다.

⇒ Column의 가로축은 crossAxis, Row의 가로축은 mainAxis인 것으로 보인다.

(Flutter에서 Column과 Row의 cross-main, height-width 개념이 반대로 적용되는 것으로 보인다.)

- 콤마를 붙이고 저장하면 예쁘게 포맷팅된다.
- 원하는 Widget의 ClassName을 선택하고 Code Action을 이용하면 Wrap with Padding을 사용하여 Padding으로 감쌀 수 있다.
