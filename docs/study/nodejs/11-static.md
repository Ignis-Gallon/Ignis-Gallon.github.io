---
title: "static"
date: 2022-11-25
last_modified_at: 2022-11-25
category: study
tags: [Node.js, Express, 정적 파일, 미들웨어]
---

# static

작성일 2022-11-25
{ .post-date }

## Static File (정적 파일)

정적 파일이란, 직접 값에 변화를 주지 않는 이상 변하지 않는 파일을 의미한다. 예를 들면, image, css 파일, js 파일 등을 의미한다. express는 이러한 정적 파일들을 손쉽게 제공할 수 있는 기능을 가지고 있다. 만약, 기능을 제공하지 않는다면 파일이 존재하는 path로 접근해왔을 때 파일을 제공해주면 된다. 하지만 이는 굉장히 번거롭고, 코드를 복잡하게 만든다.

## 정적 파일 제공하기

```javascript
app.use(express.static('public')); // express에 내장된 모듈이다.
```

express 변수에는 static이라는 메서드가 포함되어 있다. 이 메서드를 미들웨어로서 로드해준다. static의 인자로 전달되는 ‘public’은 directory의 이름이다. 따라서, ‘public’이라는 디렉터리 밑에 있는 데이터들은 웹 브라우저의 요청에 따라 서비스를 제공할 수 있다.

예를 들어, 사용자가 [localhost:3000/images/cat.jpg](http://localhost:3000/images/cat.jpg) 로 접근한다면, 해당 파일을 public/images/cat.jpg에 존재하는지 검색하게 된다. 이와 같은 방법은 public에 저장된 파일만을 제공한다는 점에서 보안적인 이점이 있다.

만약 여러 폴더를 허용하고 싶다면 다음과 같이 미들웨어를 두 번 사용하면 된다.

```javascript
app.use(express.static('public'))
app.use(express.static('files'))
```

```javascript
app.use('/static', express.static('public'));
```

때로는 가상의 경우를 사용해야 할 때도 있는데, 가상 경로를 사용한 경우, 사용자가 public 디렉터리에 있는 파일들에 접근하려면 다음과 같이 접근해야 한다.

```text
http://localhost:3000/static/images/kitten.jpg
http://localhost:3000/static/css/style.css
http://localhost:3000/static/js/app.js
http://localhost:3000/static/images/bg.png
http://localhost:3000/static/hello.html
```

express.static 메서드는 node 프로세스가 실행되는 디렉터리에 상대적이다. 따라서, 절대경로를 사용하는 것이 안전할 수 있다.

```javascript
app.use('/static', express.static(__dirname + '/public'));
```

__dirname은 현재 실행 중인 폴더 경로, 위 예제는 상대 경로이다.
