---
title: "6.10 Hero"
date: 2023-03-02
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, Hero, 애니메이션]
---

# 6.10 Hero

- Hero Widget : 화면을 전환할 때 굉장히 멋진 애니메이션을 제공한다. 이를 이용하면 원래 이미지를 덮어버리는 것이 아니라 포스터를 움직여버리는 역할을 한다.
- Hero Widget을 두 개의 화면에 각각 사용하고 각각의 위젯에 같은 태그를 주기만 하면, Flutter가 알아서 해준다.
- Webtoon Widget에서 포스터를 Hero Widget으로 감싸주고, tag를 ID로 달아준다.
- 마찬가지로, Detail_screen에서 이미지에 해당하는 Container를 감싸서 tag를 ID로 달아주자.

⇒ 이렇게 하면 이미지가 옆으로 이동하는 것처럼 보이게 된다.
