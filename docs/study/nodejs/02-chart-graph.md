---
title: "Chart를 이용한 Graph"
date: 2022-11-18
last_modified_at: 2022-11-18
category: study
tags: [Node.js, Chart.js, 서버 사이드 렌더링, 클라이언트 사이드 렌더링]
---

# Chart를 이용한 Graph

작성일 2022-11-18
{ .post-date }

- `Client` ↔ `Server` ↔ `DB Server`

- `그래프를 표현하는 두 가지 방법`

    1. 서버가 DB로부터 데이터를 끌어온 후, 차트를 그려서 Client에게 전달  
        → Server Side Rendering  
        → `단점` : Server Side에서의 load가 심해진다.  
        → `장점` : Computing 능력이 없는 Client도 간단히 이용 가능하다.
    2. 클라이언트에서 데이터를 전달 받아서 차트를 그린다. (Raw Data)  
        → Client Side Rendering  
        → `단점` : Client Side의 load가 심해진다.  
        → `장점` : Server Side에서의 load가 줄어든다.

이전 까지는 Client는 정적인 페이지였다. JS를 이용한 동적인 프로그래밍이 불가능 했기 때문에, 서버에서 연산을 한 뒤, 연산 결과를 Client에 보내주는 형식이였다면 이제는 Server Side에서  
Client에 Raw Data를 보내준 뒤, Client에서 데이터 연산을 진행한다.

- 우리 예제에서는 Front End와 Back End를 모두 Javascript (node.js)를 이용한다.

    → 규모가 작은 서비스에서는 Front와 Back을 한 개발자가 맡아서 개발하는 것이 좋다.

- [Chartjs.org](http://Chartjs.org) → chart를 그리는 javascript library
