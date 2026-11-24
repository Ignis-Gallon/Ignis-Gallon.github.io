---
title: "Request Properties(Body-Parser)"
date: 2022-11-25
last_modified_at: 2022-11-25
category: study
tags: [Node.js, Express, req.params, req.query, body-parser]
---

# Request Properties(Body-Parser)

작성일 2022-11-25
{ .post-date }

> 참고  
>   
> [https://studyingych.tistory.com/34](https://studyingych.tistory.com/34)  
>   
> [https://discuss.codecademy.com/t/whats-the-difference-between-req-params-and-req-query/405705](https://discuss.codecademy.com/t/whats-the-difference-between-req-params-and-req-query/405705)

## Request Properties

---

- req.params

예를 들어서 /user/:name 이라는 경로가 있다면, “name” 속성을 req.params.name 으로 api에서 사용할 수 있다. `https://params/user/12341234`일 경우 12341234를 전달받을 수 있다.

---

- req.query

이 속성은 경로의 각 쿼리 문자열 매개 변수에 대한 속성이 포함된 개체이다. 무슨 말이냐면,

`https://query/search?searchWord=구글검색` 이라고 한다면, ‘searchWord’ 매개변수(parameter)의 ‘구글검색’ 이라는 값(argument)을 가져오는 것이다.

const path = process.cwd()

만약 express api를 이용한다고 하면 location.href=path+”/public/search?searchWord=구글검색 이런 식으로 구성해서 req.query로 떼오는 방식을 사용할 수 있을 것이다.

만약 .js 파일에서 location.href를 사용해서 A page → B page로 간다고 해보자.

A : location.href = “B?”+value+”:”

이런 식으로 넘겨주었다고 하자. 그러면,

B : const temp = location.href.split(”?”) // 우선적으로 ?를 기준으로 split하면, ?를 기준으로 temp에 배열이 생성된다. 이렇게 되면, temp[0] = ? 이전까지에 나오던 주소, temp[1] = value: 가 된다.

그 뒤, const data = temp[1].split(”:”) 라고 하면, 다시 한 번 : 를 기준으로  split되어서 배열이 data에 생성될 것이다.

현재는 데이터를 하나만 주었기 때문에 data가 value하나만 있지만, 여러 개를 넘겨 준다면 data[i]로 접근하게 될 수 있을 것이다.

ex)

from the route: /lor/creatures/hobbit?familyname=Baggins&home=Shire

then, we get two query parameters, familyname and home and so our object would look like:

```javascript
{ //req object
  params: {
    name: 'hobbit'
  },
  query: {
    familyname: 'Baggins',
    home: 'Shire'
  }
...
}
```

---

- req.body

req.body는 데이터를 담을 때 사용한다. (주로 post로 유저의 정보 or 파일 업로드를 보냈을 때)  
요청 본문에 제출 된 key-value 데이터 쌍을 포함한다. 기본적으로 이는 정의되어 있지 않기 때문에

```javascript
express.json()
express.urlencoded()
```

와 같은 미들웨어를 사용해야 한다.

다시 말하자면, req.body는 body-parser를 사용하기 전에는 default 값으로 Undefined가 나오기 때문에 body-parser를 사용해야 오류가 나타나지 않는다.

(단, body-parser를 이용하면 Raw, Text형식의 본문을 추가로 해석할 수 있음.)

extends 옵션 설명

⇒ [https://stackoverflow.com/questions/29960764/what-does-extended-mean-in-express-4-0/45690436#45690436](https://stackoverflow.com/questions/29960764/what-does-extended-mean-in-express-4-0/45690436#45690436)

true :  URL-encoded data가 qs library로 parse된다.

false : URL-encoded data가 query-string library로 parse된다.

> form에서 데이터를 받아서 POST로 전송하면, api에서 req.body.property로 참조 가능하다.
