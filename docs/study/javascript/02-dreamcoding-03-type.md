---
title: "드림코딩 3: type"
date: 2022-12-22
last_modified_at: 2022-12-24
category: study
tags: [JavaScript, 드림코딩, 변수, 데이터 타입, 호이스팅]
---

# 드림코딩 3: type

작성일 2022-12-22 · 최종 수정일 2022-12-24
{ .post-date }

프로그래밍에서 가장 중요한 것은 무엇인가?

입력 - 연산 - 출력

이라는 일련의 과정이라고 볼 수 있다. 특정한 입력을 받으면 적절한 연산 과정을 거쳐서 다시 사용자에게 출력해주는 것이 가장 중요한 과정이라고 볼 수 있는데, 이 과정에서 CPU를 가장 효율적으로 사용할 수 있는 연산을 구성하는 것이 중요하다. (메모리 사용 최소화, CPU 최적화 로직 구성)

---

- 변수(let)

현재 자바스크립트에서는 ES6에 추가된 변수 타입인 let을 사용한다. 일반적으로 자바스크립트는 최신 버전 이전의 언어는 사용하지 않는다.

```javascript
'use strict';

// variable
// let (added in ES6)
// ES6 이전의 문법은 거의 사용하지 않는다.

let name = 'ellie';
console.log(name);
name = 'hello';
console.log(name);
```

모든 프로그램은 메모리를 할당받는데, 우리가 프로그램에서 변수를 만들게 되면 그 변수는 메모리의 특정 공간을 가리키는 포인터가 된다. 변수가 가리키는 공간에 가보면, 우리가 할당한 값이 보관되어 있다.

---

- Block Scope (지역 변수)

```javascript
'use strict';

// variable
// let (added in ES6)
// ES6 이전의 문법은 거의 사용하지 않는다.

let globalName = "jdk";

{
    let name = 'ellie';
    console.log(name);
    name = 'hello';
    console.log(name);   
}

// name은 block scope에서 선언된 변수이므로
// 현재는 선언되지 않았다고 판단되기 때문에 출력할 수 없음.
// error 발생...
// console.log(name);
console.log(globalName);
```

Global Scope에서 선언한 변수는 프로그램이 동작하는 동안 계속해서 메모리 상에 존재하기 때문에, 가능하면 개수를 줄이는 것이 좋다.(최소화) 가능하다면, 클래스나 함수, if나 for loop에서 정의해서 사용하는 것이 좋다. javascript에서 변수를 선언할 수 있는 유일한 키워드는 `let`이다.

- var : 이전에는 var라는 키워드를 사용했는데, 이제는 var를 절대 사용하지 않는다. 왜 그러냐면, 이전까지의 자바스크립트는 변수를 선언하기도 전에 값을 할당하고, 값을 쓰고나서 나중에 변수를 선언하는 비정상적인 동작이 가능했다. 이는 매우 위험한 행동이기 때문에, 이제는 `let`을 사용한다.

⇒ 이를 `var hoisting`이라고 한다. hoisting이란, 어디에 선언되었는지에 상관없이 항상 제일 위로 선언을 끌어올리는 것을 말한다. (영단어 뜻 : 끌어올려주다.) var를 Global Scope로 선언하면, 파일의 가장 위에서 선언되는 것과 같은 효과를 보인다. (단, 값의 할당은 별개)

⇒ 또한, `var`는 Block Scope를 무시한다. 어떤 지역 변수로 선언하더라도, 항상 언제 어디서든 볼 수 있다. 이는 프로그램의 유연성으로 볼 수도 있지만, 프로젝트의 규모가 커지면 굉장히 위험하게 사용된다는 단점이 있기 때문에 let을 사용하게 되었다.

---

- 상수 (Constants)

constant는 선언이 되면, 메모리를 가리키는 포인터가 잠긴다. 따라서, 한 번 값이 할당되고 나서는 값을 절대 변경할 수 없다. 값을 계속 변경할 수 있는 것을 mutable data type이라고 하는데, 이는 let이 해당하는 것이고 constant는 변경이 불가능하기 때문에 immutable data type이라고 한다.

> favor immutable data type always : 되도록이면 값이 바뀌지 않는 타입의 데이터를 써라.

- security(보안상의 이유, 해커들로부터 보호)
- thread safety(프로세스 안에서는 여러 스레드가 동시에 돌아가는데, 스레드들이 동시에 변수에 접근해서 값을 바꾸면 synchronize가 되어있지 않은 이상 잘못된 동작이 발생할 수도 있다.)
- reduce human mistakes

---

- variable types
    1. primitive type : number, string, boolean, null, undefined, symbol
    2. object(single item을 한 단위로 묶어서 관리하는 것, box container)
    3. function(function도 하나의 변수로 할당이 가능하다. 즉, 함수의 인자로 전달도 가능하며, return type으로 설정도 가능하다. first-class function이라고 부른다.)

자바스크립트는 숫자를 다루는 타입이 number 하나만 있으며, number 변수를 선언할 때 따로 type을 지정해주지 않고 let만 작성하면 된다. 자바스크립트는 type이 dynamic한 언어이기 때문에 알아서 선언이 된다.

```javascript
const infinity = 1/0; 
// infinity

const minus_inifinity = -1/0; 
// -infinity

const not_a_number = "not a number" / 2; 
// NaN(Not a Number)
```

string : 자바스크립트에서는 한 글자이던 여러 글자이던 문자는 다 string으로 처리된다.  
backtick을 이용하고, curly braces를 이용하면 문자열에 변수를 쉽게 출력할 수 있다.

```javascript
const char = 'c';
const brendan = 'brendan';
const greeting = 'hello' + brendan;
console.log(`value: ${greeting}, type: ${typeof greeting}`);
const helloBob = `hi ${brendan}!`; // template literals (string)
console.log(`value: ${helloBob}, type: ${typeof helloBob}`);
```

Symbol : map이나 다른 자료구조를 사용할 때 고유한 식별자가 필요한 경우, 아니면 동시 다발적으로 일어날 수 있는 코드에서 concurrent(우선 순위)를 주고싶을 때 정말 고유한 식별자가 필요할 때 사용할 수 있다. 간혹 식별자를 string으로 사용하는 경우가 있는데, 다른 모듈이나 파일에서 동일한 string을 사용하면 동일한 식별자로 판단되지만 Symbol은 서로 다르다.

```javascript
const symbol1 = Symbol('id');
const symbol2 = Symbol('id');
console.log(symbol1 === symbol2);
```

두 개의 symbol이 같은지 아닌지 구분하려면, 위와 같이 직접 값을 비교하고 찍어봐야 한다. 

```javascript
const gSymbol1 = Symbol.for('id');
const gSymbol2 = Symbol.for('id');
console.log(gSymbol1 === gSymbol2);

console.log(`value : ${gSymbol1.description}, type : ${typeof gSymbol1}`);
```

이 예제처럼 Symbol.for를 사용하면, string이 같을 경우 같은 Symbol로 취급되도록 만들 수 있다.

Symbol은 바로 출력하면 에러가 발생하며, 항상 Symbol.description을 이용해 string으로 변환하고 출력해줘야 에러가 발생하지 않는다.

---

- object

```javascript
const myObj = { name: 'ellie', age: 20 };
```

const로 object를 구성할 경우, myObj자체는 const이기 때문에 const에 새로운 값을 넣을 수는 없다. 하지만, 그 안에 들어있는 변수인 name과 age는 const가 아니기 때문에 myObj.name, myObj.age와 같은 방식으로 접근해서 값을 바꿀 수 있다.

---

- Dynamic Typing : dynamically typed language

자바스크립트는 선언할 때 어떤 타입인지 선언하지 않고, runtime에 (프로그램이 동작할 때) 할당된 값에 따라서 값이 변경될 수 있다. 좋은 아이디어가 있다면, 빠르게 프로토타입을 만들어내는데는 매우 좋지만, 다수의 엔지니어들이 참여하는 규모가 큰 프로젝트는 dynamic typing 때문에 문제가 발생할 수도 있다.
