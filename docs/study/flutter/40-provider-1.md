---
title: "provider 1"
date: 2023-07-09
last_modified_at: 2023-07-09
category: study
tags: [Flutter, Provider, ChangeNotifier, Consumer]
---

# provider 1

작성일 2023-07-09
{ .post-date }

## Basics of provider package

- ChangeNotifier : ChangeNotifier is a data holder class.

A class is extends the ChangeNotifier so that particular class it be holding our data like if you see xyz things like that. (ChangeNotifier를 상속하는 Class는 데이터를 보관하는 클래스이다.)

- Consumer(User) : It’s going to use the data.
- Provider.of&lt;&gt;() : Interaction with the user (which means it does interaction from ui to the user) and data holder. (즉, 유저와 데이터 홀더인 ChangeNotifier가 상호작용 할 수 있도록 돕는 것)
- ChangeNotifierProvider : App의 Entry에서 설정해주어야 함.
