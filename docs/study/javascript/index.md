---
title: "javascript"
---

# javascript

웹 개발을 하면서 JavaScript 기초를 다시 공부하며 정리한 노트입니다. 2022년 12월에 드림코딩 강의를 들으며 적은 기록과 2023년 1월에 생활코딩 강의를 보며 적은 기록, 그리고 2023년 1월과 7월에 class와 id, 스코프 체이닝, 동작 원리, AJAX를 찾아보며 쓴 글을 묶었습니다.

**드림코딩**

- [드림코딩 2: async defer](01-dreamcoding-02-async-defer.md): 스크립트를 불러오는 네 가지 방식(head, body 끝, async, defer)의 장단점, 'use strict'
- [드림코딩 3: type](02-dreamcoding-03-type.md): let과 var, 호이스팅, 블록 스코프, const와 불변 데이터 타입
- [드림코딩 5: function, arrow function](03-dreamcoding-05-function.md): 기본값 매개변수, rest parameters, early return, 일급 함수와 익명 함수
- [드림코딩 6: class, object](04-dreamcoding-06-class-object.md): ES6 class, getter와 setter, public과 private, static, 상속, instanceof
- [드림코딩 7: object](05-dreamcoding-07-object.md): object 생성과 프로퍼티 접근, literal property와 computed property, 복사
- [드림코딩 8: array](06-dreamcoding-08-array.md): forEach의 콜백 인자, push, pop, unshift, shift, splice, concat
- [드림코딩 9: array APIs](07-dreamcoding-09-array-apis.md): join, split, reverse, find, map, some, every, reduce
- [드림코딩 10: JSON](08-dreamcoding-10-json.md): HTTP 요청과 응답, AJAX와 XHR, JSON의 특징과 직렬화
- [드림코딩 11: callback](09-dreamcoding-11-callback.md): 동기와 비동기, 동기 콜백과 비동기 콜백, 콜백 지옥
- [드림코딩 12: Promise](10-dreamcoding-12-promise.md): Promise의 상태, producer와 consumer, then, catch, finally, chaining
- [드림코딩 13: async-await](11-dreamcoding-13-async-await.md): async와 await, 병렬 처리, Promise.all과 Promise.race

**생활코딩**

- [생활코딩: callback function](12-opentutorials-callback-function.md): 일급 객체로서의 함수와 콜백 함수, 화살표 함수로 줄여 쓰기
- [생활코딩: Promise](13-opentutorials-promise.md): 동기와 비동기의 차이, fetch가 반환하는 Promise, Promise chaining
- [생활코딩: async/await](14-opentutorials-async-await.md): 타이머 예제를 콜백, Promise chaining, await 순서로 바꿔 보기

**단독 노트**

- [class와 id의 차이](15-class-vs-id.md): 불러오는 방식, 중복 사용 여부, 한 요소에 붙일 수 있는 개수

**Study**

- [Scope Chaining](16-scope-chaining.md): 가까운 블록부터 변수를 찾는 규칙, 함수를 반환해 내부 변수를 바깥에서 쓰기
- [자바스크립트 동작원리](17-how-javascript-works.md): Stack, Heap, Event Queue의 역할과 단일 스레드 특성
- [AJAX](18-ajax.md): 새로고침 없이 서버에 요청을 보내는 기술, CORS 에러와 Access-Control-Allow-Origin
