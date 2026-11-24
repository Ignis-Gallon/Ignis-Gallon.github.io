---
title: "웹 기초"
---

# 웹 기초

2023년 1월 말부터 3월 사이, Spring으로 작성된 서버를 Node.js로 옮기는 작업을 하면서 정리한 노트입니다. 처음 접한 Spring의 구조와 용어를 먼저 정리했고, 화면 스크립트와 서버 코드를 옮기면서 찾아본 JavaScript와 Node.js의 기초 내용을 주제별로 모았습니다. 이 작업의 과정은 프로젝트 글 [Spring → Node.js 서버 전환](../../projects/server/01-spring-to-nodejs.md)에 정리했습니다.

**Spring**

- [Spring MVC 구조(Controller, Service, DAO, Mapper)](01-spring-mvc-structure.md): MVC 구조와 계층별 역할, 요청이 DB까지 전달되는 흐름, 계층별 작성 예시
- [의존성 주입(DI), IoC 컨테이너, @Autowired, @Resource](02-dependency-injection-ioc.md): 의존 객체를 외부에서 주입받는다는 개념, Bean과 컨테이너, 타입으로 찾는 주입과 이름으로 찾는 주입

**JavaScript와 Node.js**

- [Ajax, Axios, Fetch](03-ajax-axios-fetch.md): 브라우저에서 서버와 비동기로 통신하는 방법 세 가지의 비교
- [jQuery와 DOM, form의 hidden 값, POST](04-jquery-dom-form-hidden-post.md): jQuery와 DOM의 개념, hidden 입력으로 값을 넘기는 이유, GET과 POST의 전달 위치 차이
- [window.onload](05-window-onload.md): 문서가 모두 준비된 뒤에 스크립트를 실행하게 하는 방법
- [this 바인딩](06-this-binding.md): this가 가리키는 대상, call(), 생성자와 클래스, 화살표 함수의 this
- [ejs 템플릿](07-ejs-template.md): 코드를 넣는 태그와 변수값을 넣는 태그, 서버가 넘긴 변수 쓰기, 사용할 때 유의할 점
- [async/await로 DB 쿼리하기](08-async-await-db-query.md): Promise를 직접 만드는 방식과 Promise를 돌려주는 드라이버, 다중 쿼리
