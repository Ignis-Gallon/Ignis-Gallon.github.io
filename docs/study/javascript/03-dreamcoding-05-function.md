---
title: "드림코딩 5: function, arrow function"
date: 2022-12-22
last_modified_at: 2022-12-22
category: study
tags: [JavaScript, 드림코딩, 함수, 화살표 함수, 콜백]
---

# 드림코딩 5: function, arrow function

작성일 2022-12-22
{ .post-date }

프로그램 안에서도 각각 저마다의 기능을 수행하는 함수들이 있는데, procedural programming 같은 경우에는 함수가 굉장히 중요한 기능을 담당한다. 그런데, 자바스크립트는 클래스가 추가되어서 OOP가 아닌가? 라고 생각할 수 있지만, 사실 자바스크립트는 프로토타입을 베이스로한 가짜의 OOP이다. 그래서 사실 자바스크립트도 일종의 절차지향형 프로그래밍 언어라고 볼 수 있다.

그래서, function이 굉장히 중요한 기능을 담당하기 때문에 때로는 sub-program이라고도 부른다. function은 프로그램을 구성하는 가장 기본적인 building block이며, 재사용이 가능하다는 특징이 있다. 

- 자바스크립트에서 function을 정의하는 방법

```javascript
'use strict';

// 자바스크립트에서의 함수 선언
// function name(param1, param2) { body... return; }
```

함수는 한 번에 한 가지의 일만 해야하며, 이름은 doSomething과 같은 command의 형태로 구성해야 한다. 

자바스크립트에서 function은 object이다. 

---

```javascript
function changeName(obj) {
    obj.name = 'coder';
}

const ellie = { name: 'ellie'};
changeName(ellie);
console.log(ellie);
```

이와 같은 함수를 만들고 동작시켰을 때, 우리는 전달된 ellie라는 const가 실제 메모리 공간을 가리키는 포인터로 동작하기 때문에 function 내에서 값을 바꿔주면 실제로 ellie object 내부의 값이 바뀌게 된다.

- default parameter(added in ES6)

```javascript
// default parameter
function sohwMessage(message, from = 'Unknown') {
    console.log(`${message} by ${from}`);
}

sohwMessage('Hi!');
```

- Rest parameters(added in ES6) : 가변 인자

```javascript
function printAll(...args) {
    for (let i = 0; i < args.length; i++) {
        console.log(args[i]);
    }

    for (const arg of args) {
        console.log(arg);
    }

    args.forEach((arg) => {
        console.log(arg);
    })
}

printAll('dream', 'coding', 'ellie');
```

인자를 …args와 같이 선언하면 인자가 배열로 넘어온다. 인자의 개수에 상관없이 전달받을 수 있다. 추가로, 아래 두 가지 방법을 이용해서 간단하게 반복문을 사용할 수도 있다. (forEach는 함수형 언어)

이전에 function은 일종의 object라고 했는데, 그렇기 때문에 위의 예제에서는 printAll.xxx 와 같이 object처럼 attribute를 확인할 수 있다.

---

- early return, early exit

```javascript
// bad
function upgradeUser(user) {
    if (user.point > 10) {
        // long upgrade logic...
    }
}

// good
function upgradeUser(user) {
    if (user.point <= 10) {
        return;
    }

    // long upgrade logic...
}
```

코드를 구성할 때 return을 빨리 시켜주는 것이 좋음. (현업팁, 불필요한 코드 실행을 방지)

---

First-class function  
functions are treated like any other variable  
can be assigned as a value to variable  
can be passed as an argument to other functions.  
can be returned by another function  
  
1. Function expression  
a function declaration can be called earlier than it is defined. (hoisted)  
a function expression is created when the execution reaches it.

```javascript
const print = function () {
    console.log('print');
};

print();

const printAgain = print;
printAgain();
const sumAgain = sum;
console.log(sumAgain(1, 3));
```

function에 이름을 지정하지 않고 (Anonymous function) 필요한 부분만 선언해서 변수에 지정할 수 있다. 이름이 있는 함수를 named function이라고 한다. 함수를 print라는 변수에 선언하게 되면, 그 변수를 이용해서 함수를 호출하는 것처럼 할 수 있다.

그렇다면, function expression과 function declaration의 차이점은 무엇일까?

- function expression은 할당이 된 후에 사용할 수 있다.
- 반면, function declaration은 할당이 되기 전부터 사용할 수 있다. (마치 var처럼 hoisting된다.)
    ⇒ 자바스크립트 엔진이 선언된 함수를 가장 위로 끌어올려주기 때문이다.

---

- callback Hell

함수를 전달해서 상황에 맞는 함수를 부르라고 하는 함수를 callback function이라고 한다.

```javascript
function randomQuiz(answer, printYes, printNo) {
    
    if (answer === 'love you') {
        printYes();
    } else {
        printNo();
    }
}

const printYes = function () {
    console.log('yes!');
};

const printNo = function print() {
    console.log('no!');
};

randomQuiz('wrong', printYes, printNo);
randomQuiz('love you', printYes, printNo);
```

이 예제에서는 printYes, printNo라는 callback function이 parameter로 넘어오고, answer가 특정 값일 경우에만 printYes()를 실행, 아닌 경우 printNo()를 실행한다.

expression에서 이름을 쓸 경우(printNo, named function)는 debugging을 할 때 stack trace에 함수 이름이 나오게 하기 위해서 하는 것이고, 또는 함수 안에서 함수 자기 자신을 부르는 경우(recursion, 재귀호출)에 사용한다. 

---

- Arrow function : arrow function은 항상 이름이 없는 anonymous function이다.

```javascript
const simplePrint = () => {
    console.log('simplePrint!!!');
}

const add = function (a, b) { return (a + b) }; // function keyword를 사용할 때
const addArrow = (a, b) => a+b; // arrow function
// 만약, { }으로 감싸게 될 경우에는 반드시 return keyword를 명시해서 사용해야 한다.
```
