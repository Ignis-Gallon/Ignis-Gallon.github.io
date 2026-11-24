---
title: "conio.h: 콘솔 키 입력 함수"
date: 2023-05-04
last_modified_at: 2023-05-04
category: major
tags: [운영체제, C, conio.h, 키 입력]
---

# conio.h: 콘솔 키 입력 함수

작성일 2023-05-04
{ .post-date }

int kbhit() : 키보드를 눌러 버퍼에 data가 있으면 1을, 없으면 0을 반환한다.

int getch() : 값을 입력할 때까지 기다리고 값이 들어오면 버퍼에서 바로 값을 꺼내 리턴 ‘예외 키’를 누를 경우, 값이 두 개가 된다. 확장키들을 구분하는 방법은 getch 함수를 다시 한 번 호출하면 알 수 있다.

int getche() : getch와 동일한 기능이지만, 에코 기능이 있다.

int ungetch(int _ch) : 버퍼에 변수 _ch(data)를 push(입력) 한다.
