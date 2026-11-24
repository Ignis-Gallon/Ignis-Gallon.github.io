---
title: "node.js"
---

# node.js

Node.js와 Express로 웹서버를 만드는 법을 공부하며 정리한 노트입니다. 2022년 11월에 kodejun 실습 코드를 따라 치며 적은 기록과 Node.js 디자인 패턴 강의 노트, 그리고 2022년 11월부터 2023년 3월 사이에 모듈 시스템, Express 미들웨어, JWT를 찾아보며 쓴 글을 묶었습니다.

**kodejun 실습**

- [Express 웹서버 만들기](01-express-web-server.md): npm init과 express 설치, 로그인·사용자 추가 서버 코드
- [Chart를 이용한 Graph](02-chart-graph.md): 서버 사이드 렌더링과 클라이언트 사이드 렌더링 비교, Chart.js
- [Image DB](03-image-db.md): 이미지를 파일로 둘지 DB에 넣을지, MySQL의 BLOB 타입
- [new: web_server](04-new-web-server.md): Express 웹서버 코드와 HTTP 응답 상태 코드, sendStatus()
- [new: login](05-new-login.md): 커넥션 풀로 로그인과 사용자 추가를 처리하는 서버 코드, form의 method와 action
- [new: chart_graph](06-new-chart-graph.md): DB에서 조회한 데이터를 fetch로 받아 Chart.js로 그리기

**디자인 패턴**

- [What Are Design Patterns?](07-what-are-design-patterns.md): 디자인 패턴이 무엇이고 왜 필요한가
- [Singletons](08-singletons.md): require() 캐시와 싱글턴, 함수 모듈과 클래스 모듈의 차이
- [Factories - Generic Interface for Creating](09-factories.md): 팩토리 패턴과 추상 팩토리 패턴

**study**

- [commonJS & ES module](10-commonjs-and-es-module.md): require와 import, .mjs와 type 설정, 동기·비동기 로딩
- [static](11-static.md): express.static으로 정적 파일 제공하기, 가상 경로와 절대 경로
- [path](12-path.md): path.join()과 path.resolve()의 차이
- [Request Properties(Body-Parser)](13-request-properties.md): req.params, req.query, req.body
- [app.use()](14-app-use.md): 미들웨어 등록 순서와 next(), 애플리케이션 레벨과 라우터 레벨
- [JWT(Web Token)](15-jwt.md): JSON Web Token의 개념과 헤더·페이로드·서명 구조
- [안정적인 토큰의 형태](16-stable-token-structure.md): Access Token과 Refresh Token, Redis 화이트리스트
