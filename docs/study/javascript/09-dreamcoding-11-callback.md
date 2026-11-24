---
title: "드림코딩 11: callback"
date: 2022-12-25
last_modified_at: 2022-12-25
category: study
tags: [JavaScript, 드림코딩, 콜백, 비동기]
---

# 드림코딩 11: callback

작성일 2022-12-25
{ .post-date }

자바스크립트는 Synchronous(동기식)하다. 즉, hoisting이 된 이후로, 우리가 작성한 코드의 순서에 따라 코드가 동작된다. 

- hoisting : var, function declaration (변수나 함수의 선언이 자동적으로 파일의 맨 위로 올라가는 것)

Asynchronous : 코드가 언제 동작할지 알 수 없는 것. 가장 대표적인 예로 setTimeout이라는 API가 있다. setTimeout은 지정한 시간이 지나면, 지정해두었던 callback 함수가 호출된다.

```javascript
'use strict';

console.log('1');
setTimeout(() => console.log('2'), 1000);
console.log('3');
```

이렇게 코드를 작성했을 경우, 당연하게도 출력되는 순서는 1, 3, 2가 될 것이다. (Asynchronous)

- callback

1) Synchronous callback

2) Asynchronous callback

```javascript
'use strict';

// hoisting
// function printImmediately(print) {
//     print();
// }

// hoisting
// function printWithDelay(print, timeout) {
//     setTimeout(print, timeout);
// }

console.log('1');
setTimeout(() => {
    console.log('2');
}, 1000);
console.log('3');

// Synchronous callback
// 함수의 선언은 hoisting으로 인해 함수의 선언이 맨 위로 올라간다.
function printImmediately(print) {
    print();
}

printImmediately(() => console.log('hello!'));

// Asynchronous callback
function printWithDelay(print, timeout) {
    setTimeout(print, timeout);
}

printWithDelay(() => console.log('async callback'), 2000);
```

---

- 콜백지옥 : 콜백 안에서 또 다른 콜백을 부르면서 nesting하는 형태로 코드를 작성하는 것은 굉장히 비효율적인 코드가 된다.

```javascript
// Callback Hell Example
class UserStorage {
    loginUser(id, password, onSuccess, onError) {
        setTimeout(() => {
            if (
                (id === 'ellie' && password === 'dream') ||
                (id === 'coder' && password === 'academy')
            ) {
                onSuccess(id);
            } else {
                onError(new Error('not found'));
            }
        }, 2000);
    }
    
    getRoles(user, onSuccess, onError) {
        setTimeout(() => {
            if (user === 'ellie') {
                onSuccess({ name: 'ellie', role: 'admin'});
            } else {
                onError(new Error('no access'));
            }
        }, 1000);
    }
}

// 1. ID, PW를 입력받아오고, 이를 서버에게 보냄. => 로그인
// 2. 역할을 받아옴

const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');

userStorage.loginUser(
    id, 
    password, 
    (user) => {
        userStorage.getRoles(
            user, 
            (userWithRole) => {
                alert(`Hello, ${userWithRole.name}, you have a(an) ${userWithRole.role} role`);
            }, 
            (error) => {
                console.log(error);
            }
        )
    }, 
    (error) => {
        console.log(error);
    }
);
```

콜백지옥의 문제점 : 읽기가 너무 힘들다. ⇒ 가독성이 굉장히 많이 떨어짐.

비즈니스 로직을 한 눈에 이해하기가 어렵다. 또, 나중에 에러가 발생했을 때 디버깅하거나 문제를 분석하는 것, 유지보수가 매우 어렵다.
