---
title: "드림코딩 10: JSON"
date: 2022-12-25
last_modified_at: 2022-12-25
category: study
tags: [JavaScript, 드림코딩, JSON, 직렬화]
---

# 드림코딩 10: JSON

작성일 2022-12-25
{ .post-date }

브라우저 위에서 동작하고 있는 웹사이트와 웹 애플리케이션이 어떻게 서버와 통신할 수 있는가를 정의한 규격이 바로 HTTP이다. (Hypertext Transfer Protocol) 

HTTP는 클라이언트(웹 사이트)가 웹 서버에 요청(request)을 보내면, 서버는 이에 대한 응답(response)을 보내주는 클라이언트에게 보내주는 방식으로 진행된다.

여기서 Hypertext는 전반적으로 사용되는 문서나 이미지 파일 등을 포함해서 부르는 말이다.

이전에는 AJAX(Asynchronous JavaScript And XML)을 사용해서 웹 사이트와 웹 서버가 데이터를 비동기적으로 주고 받았다. 대표적인 예로는 XHR(XMLHttpRequest)라는 것이 있다. fetch() API를 이용하면 간편하게 데이터를 주고받을 수 있다.

AJAX와 XHR에서 반복되는 것이 ‘X’인데, 여기서 X는 XML을 말한다. XML은 HTML과 같은 Mark-up 언어의 하나이다. HTML과 마찬가지로 데이터를 표현할 수 있는 한 가지 방법이다. 서버와 데이터를 주고받을 때는 굉장히 다양한 파일 포맷을 전달받을 수 있다. 요즘에는 JSON을 많이 사용한다. 

JSON(Javascript Object Notation) : 자바스크립트 오브젝트와 관련된 파일을 다룬다.

자바스크립트에서 Object는 Key와 Value로 이루어져 있다. JSON은 모바일과 서버에서 데이터를 주고받을 때, 또는 서버와 통신하지 않더라도 오브젝트를 파일 시스템에 저장할 때도 JSON 데이터 타입을 자주 사용한다.

- simplest data interchange format
- lightweight text-based structure
- easy to read
- key-value pairs
- used for serialization and transmission of data between the network the network connection
- independent programming language and platform

⇒ 언어에 상관없이, 언어가 쓰이는 플랫폼에 상관없이 JSON으로 serialization된 오브젝트를 다시 언어의 특징에 맞게 object로 변환하고, object를 다시 JSON으로 serialization하는 것을 지원해주거나 아니면 자주 사용되는 외부 라이브러리를 통해 이러한 기능을 지원해준다.

---

- 그렇다면 JSON을 어떻게 공부해야하는가?

1) object를 어떻게 serialize 하는가?

2) 전달된 JSON을 어떻게 object로 deserialize 하는가?

1) 

```javascript
// 1. Object to JSON
// JSON.stringify();

let json = JSON.stringify(true);
console.log(json);
```

```typescript
interface JSON {
    /**
     * Converts a JavaScript Object Notation (JSON) string into an object.
     * @param text A valid JSON string.
     * @param reviver A function that transforms the results. This function is called for each member of the object.
     * If a member contains nested objects, the nested objects are transformed before the parent object is.
     */
    parse(text: string, reviver?: (this: any, key: string, value: any) => any): any;
    /**
     * Converts a JavaScript value to a JavaScript Object Notation (JSON) string.
     * @param value A JavaScript value, usually an object or array, to be converted.
     * @param replacer A function that transforms the results.
     * @param space Adds indentation, white space, and line break characters to the return-value JSON text to make it easier to read.
     */
    stringify(value: any, replacer?: (this: any, key: string, value: any) => any, space?: string | number): string;
    /**
     * Converts a JavaScript value to a JavaScript Object Notation (JSON) string.
     * @param value A JavaScript value, usually an object or array, to be converted.
     * @param replacer An array of strings and numbers that acts as an approved list for selecting the object properties that will be stringified.
     * @param space Adds indentation, white space, and line break characters to the return-value JSON text to make it easier to read.
     */
    stringify(value: any, replacer?: (number | string)[] | null, space?: string | number): string;
}
```

API를 보면, replacer와 space는 ?(optional)하다. 필수 전달해줄 것은 JSON형식 string으로 바꿀 value

overloading된 두 개의 stringify API가 존재.

JSON.stringify();를 이용하면, 전달된 데이터를 JSON형식의 string으로 바꿔서 전달받을 수 있다.

- 배열과 object의 stringify

```javascript
json = JSON.stringify(['apple', 'banana']);
console.log(json);

const rabbit = {
    name: 'tori',
    color: 'white',
    size: null,
    birthDate: new Date(),
    // symbol: Symbol('id'), // cannot included in JSON string
    jump: () => {
        console.log(`${name} can jump!`);
    }
}

json = JSON.stringify(rabbit);
console.log(json);
```

jump라는 함수는 JSON에 포함되지 않는다. 함수는 object에 있는 데이터가 아니라서 JSON에서 제외되고, Symbol과 같이 자바스크립트에만 있는 데이터는 JSON에 포함되지 않는다.

- JSON 데이터를 좀 더 통제해보기

```javascript
// replacer array사용
json = JSON.stringify(rabbit, ['name', 'color']);
console.log(json);
```

⇒ 위와 같이 stringify할 데이터 뒤에 ,을 찍고 배열 형태로 string을 전달해주면, 해당 string에 매칭되는 key값과 value만 JSON으로 stringify된다.

```javascript
// replacer 콜백 함수
json = JSON.stringify(rabbit, (key, value) => {
    console.log(`key : ${key}, value : ${value}`);
    return value;
});
console.log(json);

console.clear();
json = JSON.stringify(rabbit, (key, value) => {
    console.log(`key : ${key}, value : ${value}`);
    return key === 'name' ? 'ellie' : value;
})
console.log(json);
```

⇒ stringify를 할 때 array로 key를 지정하는 것이 아닌 콜백 함수를 전달해주는 형태로 데이터의 제어가 가능하다. 먼저, 첫 번째로 어떻게 동작하는지 확인하기 위해 console.log를 찍어본다.

(log를 찍으면, 모든 object의 원소들에 대해서 출력된다. 심지어는 JSON에 포함되지 않는 함수도 일단은 출력된다.)

두 번째로, return할 때 key가 name인 경우는 ellie로 바꾸고, 나머지는 기본 값을 준다고 지정하면, JSON stringify가 될 때 key가 name인 항목의 value는 ellie가 된다.

(조금 더 세밀하게 통제하고 싶을 때 사용)

---

2)

```javascript
// 2. JSON to Object
// parse(JSON);

console.clear();
const obj = JSON.parse(json);
console.log(obj);

rabbit.jump();
// obj.jump();
```

JSON을 다시 object로 변환하는 것은 매우 간단하다. json을 parse라는 API를 사용해서 object로 변환하면 된다. 그런데, 기존에 사용된 rabbit은 jump()라는 함수를 갖고 있었지만, serialize되는 과정에서 jump()는 포함되지 않았으므로, JSON → object로 변환된 obj에는 jump가 포함되지 않는다.

```javascript
console.log(rabbit.birthDate.getDate());
console.log(obj.birthDate.getDate());
```

추가로, 기존에 rabbit에 있던 birthDate는 Date타입의 데이터이기 때문에 getDate()를 통해서 날짜를 가져오는 것이 가능했다. (2022.12.25 ⇒ 25가 출력됨)

하지만, obj는 JSON string을 다시 string으로 바꾼 것이므로, getDate()를 적용할 수 없다. (Date 타입이 아니고 string 타입이기 때문)

그래서 사용되는 것이, 바로 parse의 overloaded API이다. 

```typescript
    /**
     * Converts a JavaScript Object Notation (JSON) string into an object.
     * @param text A valid JSON string.
     * @param reviver A function that transforms the results. This function is called for each member of the object.
     * If a member contains nested objects, the nested objects are transformed before the parent object is.
     */
    parse(text: string, reviver?: (this: any, key: string, value: any) => any): any;
```

reviver callback 함수를 전달해주면, 이런 문제를 해결할 수 있다.

```javascript
console.clear();
const obj = JSON.parse(json, (key, value) => {
    console.log(`key : ${key}, value : ${value}`);
    return key === 'birthDate' ? new Date(value) : value;
});

console.log(rabbit.birthDate.getDate());
console.log(obj.birthDate.getDate());
```

위와 같이 key가 birthDate일 경우, 새로운 Date 객체를 만들고 object에 전달해준다고 하면, getDate()가 적용 가능한 Date 타입의 객체로 변환된다.

---

- JSON 다룰 때 유용한 사이트

JSON Diff (받아온 데이터가 뭐가 다른지 비교할 수 있는 사이트)

JSON Beautifier (JSON을 서버에서 받아오면, format이 망가지는 경우가 있다. 이 때 사용하면 포맷을 예쁘게 만들어줄 수 있다.)

JSON Parser (JSON을 object 형태로 확인해보고 싶을 때, object 형태로 어떻게 표기되는지 바꾸어서 표현해준다.)

JSON Validator (JSON이 유효한 형식인지 아닌지 검사해준다.)
