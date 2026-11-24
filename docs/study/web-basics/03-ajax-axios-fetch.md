---
title: "Ajax, Axios, Fetch"
date: 2023-03-12
last_modified_at: 2023-03-12
category: study
tags: [JavaScript, Ajax, Axios, Fetch]
---

# Ajax, Axios, Fetch

작성일 2023-03-12
{ .post-date }

## Ajax(Asynchronous JavaScript And XML)

자바스크립트를 사용해서 클라이언트와 서버가 비동기 통신을 통해 데이터를 주고받는 기술이다. Ajax를 통해 서버와 비동기적으로 통신함으로 인해 우리는 전체 웹페이지를 다시 불러오는 동기방식과는 다르게 점진적으로 해당 부분의 사용자 인터페이스를 갱신할 수 있다. 간단하게 말해서, Ajax는 JS에서 비동기 HTTP 통신이 가능하도록 해준다. → 비동기 HTTP 통신을 통해 response와 request를 비동기 식으로 다룰 수 있게 된다.

- jQuery와 묶여서 불리는 경우가 많은데, 어떻게 연관이 있는가?

보통의 상황에서 Ajax라는 말을 사용할 때를 보면, JQuery의 Ajax를 일컫을 때가 많다. 그런데 이것은 잘못된 것이며, Ajax를 JQuery를 통해 보다 더 쉽게 사용할 수 있기 때문에 JQuery와 Ajax를 함께 묶어서 말하는 경우가 많은 것 뿐이다.

- 순수 Ajax

```javascript
// use Ajax without Jquery

function reqListener (e) {
    console.log(e.currentTarget.response);
}

var oReq = new XMLHttpRequest();
var serverAddress = "https://jsonplaceholder.typicode.com/posts";

oReq.addEventListener("load", reqListener);
oReq.open("GET", serverAddress);
oReq.send();
```

- JQuery를 통해 Ajax 사용

```javascript
// use Ajax with Jquery

var serverAddress = 'https://jsonplaceholder.typicode.com/posts';

// jQuery의 .get 메소드 사용
$.ajax({
    url: ,
    type: 'GET',
    success: function onData (data) {
        console.log(data);
    },
    error: function onError (error) {
        console.error(error);
    }
});
```

- JQuery를 통해 Ajax를 사용할 때가 코드가 더 직관적이고 간단해진다. 그리고 그냥 Ajax만을 사용할 때는 브라우저 간 호환성에 대해 염두에 두고 각기 다른 코드를 작성해야하는 경우가 있는데, JQuery를 사용하면 호환성에 대해서도 보장이 된다.

---

## Axios

기존에 웹에서 사용하던 XMLHttpRequest는 잘 디자인되어 있는 API가 아니다. 요청의 상태나 변경을 구독하려면 Event를 등록해서 변경사항을 받아야만 했고, 요청의 성공, 실패 여부나 상태에 따라 처리하는 로직이 들어가기 좋지 않았다. 이를 보완하기 위해서 HTTP 요청에 최적화되어 있고 상태도 잘 추상화되어 있는 API들이 생겨나기 시작했다. 대표적으로 Axios와 fetch가 그 예인데, 그 중 Axios를 먼저 살펴본다.

```javascript
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Yongseong',
    lastName: 'Kim'
  }
});
```

## fetch

fetch는 ES6부터 JavaScript의 내장 라이브러리로 들어왔다. promise 기반으로 만들어졌기 때문에 Axios와 마찬가지로 데이터를 다루는데 어렵지 않고, 내장 라이브러리라는 장점으로 인해 상당히 편리하다. 코드 또한 간단하다.

```javascript
//fetch
const url ='http://localhost3000/test`
const option ={
   method:'POST',
   header:{
     'Accept':'application/json',
     'Content-Type':'application/json';charset=UTP-8'
  },
  body:JSON.stringify({
  	name:'sewon',
    	age:20
  })

  fetch(url,options)
  	.then(response => console.log(response))
```

## Axios vs Fetch

Axios

- 장점
    - response timeout 처리 방법이 있다. (fetch에는 존재하지 않는 기능)
    - promise 기반으로 다루기가 쉽다.
    - 크로스 브라우징에 신경을 많이 썼기 때문에 브라우저 호환성이 뛰어나다.

- 단점
    - 모듈 설치가 필요하다.
    - fetch 보다는 속도가 느리다.

Fetch

- 장점
    - 내장 라이브러리이기 때문에 별도의 import가 필요하지 않다.
    - promise 기반으로 다루기가 쉽다.
    - 내장 라이브러리이기 때문에 사용하는 프레임워크가 안정적이지 않을 때 사용하기 좋다.

- 단점
    - Internet Explorer의 경우에는 fetch를 지원하지 않는 버전도 존재한다. (브라우저 호환성이 상대적으로 떨어지는 편이다.)
    - 기능이 부족하다. JSON으로 변환해주는 과정이 필요하며, 네트워크 에러 발생 시에 response timeout이 없어 기다려야 한다.

---

## 결론

웹 프론트 프레임워크를 다룰 때에는 Axios를 사용하는 것이 더 좋다고 생각된다. Axios는 크로스 브라우징에 신경을 쓴 모듈이며, 기능 또한 우수하기 때문이다. 전반적으로 fetch의 상위 호환이라고 생각된다. 하지만, react-native에서는 fetch를 사용하는 것이 더 좋다고 생각된다. 왜냐면 react-native는 아직 안정화된 프레임워크가 아니기 때문이다.
