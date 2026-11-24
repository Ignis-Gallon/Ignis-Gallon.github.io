---
title: "6.5 waitForWebToons"
date: 2023-03-01
last_modified_at: 2023-03-04
category: study
tags: [Flutter, 노마드코더, Future, initState]
---

# 6.5 waitForWebToons

작성일 2023-03-01 · 최종 수정일 2023-03-04
{ .post-date }

- Future 데이터를 불러와서 보여주는 방법은 두 가지가 있다.
    1. 기본적인 방법
    2. 발전된 방법

- State는 UI와 변할 수 있는 데이터를 가지고 있다.
- initstate에서 웹툰 정보를 가져오는 API를 실행해주는 waitForWebToons()를 수행한다.
- 이 때, waitForWebToons()는 ApiService class에서 static method로 선언된 getTodaysToons()를 수행한다. 이로써 오늘의 웹툰 목록을 가져올 수 있다.
