---
title: "Flex 3"
date: 2023-07-23
last_modified_at: 2023-07-23
category: study
tags: [CSS, Flexbox, flex-direction, flex-wrap]
---

# Flex 3

작성일 2023-07-23
{ .post-date }

- flex-direction

display: flex; 로 설정하면, 기본값이 row이고, column으로 바꿀 수 있다.

flex-direction: row-reverse로 설정하면 역순으로 정렬되고, 반대로 flex-direction: column-reverse로 설정할 수도 있다.

- flex-wrap

flex-wrap: nowrap; 으로 설정하면, 아이템들이 절대 줄바꿈을 하지 않는다. 단, 찌그러진다.

flex-wrap: wrap; 으로 설정하면, 아이템들이 줄바꿈을 하게 된다.

⇒ 이때 align-content를 사용해서 cross-axis로 정렬할 수 있다.
