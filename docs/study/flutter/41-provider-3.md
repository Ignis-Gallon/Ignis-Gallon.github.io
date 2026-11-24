---
title: "provider 3"
date: 2023-07-09
last_modified_at: 2023-07-09
category: study
tags: [Flutter, Provider, ChangeNotifier, API]
---

# provider 3

작성일 2023-07-09
{ .post-date }

## Steps

1. Understand our data

    API를 통해 JSON 형식으로 전달됨. 네 개의 key가 있지만, 이번 예제에서는 “title”과 “body”를 사용하도록 한다.

2. Create a Data Model

    data_model.dart로 data class 생성

3. Create a Service Class for Loading a Data
4. Create a Data Class with ChangeNotifier
5. Create Provider with ChangeNotifierProvider
6. Load and Show the data
