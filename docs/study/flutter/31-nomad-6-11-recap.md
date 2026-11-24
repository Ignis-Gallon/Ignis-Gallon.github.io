---
title: "6.11 Recap"
date: 2023-03-02
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, Navigator, Hero, ListView]
---

# 6.11 Recap

- 우리의 홈페이지에 ListView를 만들었고 거기서 Webtoon이라는 widget을 렌더링하고 있다.
- widget은 커버, 즉, 포스터를 가지고 있고, 아래 타이틀 텍스트를 가지고 있다.
- ListView의 각각의 원소는 Column을 가지며, 각각의 Column은 이미지를 보여주는 Container, 간격을 만들어 줄 SizedBox, 타이틀을 보여줄 Text를 가진다. ⇒ 간단한 stateless widget
- 그리고 모든 요소를 GestureDetector로 감싸서 동작을 감지할 수 있다.

⇒ Navigator도 StatefulWidget의 일종이며, 우리가 만든 DetailScreen Widget을 지금 보는 Home 화면의 위에 올려준다. 강의에서는 MaterialPageRoute를 쓰지만, 안드로이드에서는 애니메이션이 정확히 동작하지 않아서 PageRouteBuilder를 사용한다. 하지만 실제 동작을 따져보면 실제로 페이지를 넘기는 것은 아니고, DetailPage Widget을 새롭게 띄우는 것이다. 이는 새로운 Scaffold를 return하며, AppBar를 렌더링 한다. Hero Component를 이용하면 화면 이동에 애니메이션을 줄 수 있다. 같은 ID를 입력해서 두 개의 Hero를 연결할 수 있다.
