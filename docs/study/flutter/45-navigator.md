---
title: "Navigator"
date: 2023-07-09
last_modified_at: 2024-09-19
category: study
tags: [Flutter, Navigator, Route, 화면 전환]
---

# Navigator

작성일 2023-07-09 · 최종 수정일 2024-09-19
{ .post-date }

- Route : 스마트폰에서 보여지는 단순한 하나의 페이지를 의미함.

    - App Page를 구현하기 위하여 Scaffold Widget을 return하는 Widget 하나하나가 바로 Route라고 볼 수 있다.

- Navigator는 모든 Routes(Pages)를 관리하며, Stack 형태로 Routes를 관리하게 된다. 이 과정에서 Push와 Pop 메서드를 제공한다.
- 한 페이지에서 다른 페이지로 넘어가기 위해서는, MaterialApp 아래에 다른 페이지가 child로 생성되어야 한다. (모든 Widget은 MaterialApp으로 감싸져 있어야만 한다.)

이때, Route를 관리하는 Navigator를 사용하고, 여기서 push 함수를 이용한다.

push 함수에는 context가 전달되는데, context가 갖고 있는 widget tree의 위치 정보에 근거하여 현재 화면 상에 보여지는 페이지가 어떤 페이지인지 확인하고, 이 페이지 위에 새롭게 이동하고자 하는 Route를 쌓아올려야 하기 때문이다. 즉, 첫 번째 페이지 위에 두 번째 페이지를 올리기 위해서는 첫 번째 페이지의 위치를 알아야만 하기 때문이다.

이를 구현하기 위해서는 MaterialPageRoute()가 필요하다. 왜 push 함수를 불러올 때마다 MaterialPageRoute()를 불러오고, builder를 이용해 context를 flutter의 도움을 받아 할당받아야만 할까?

⇒ 일종의 안전장치
