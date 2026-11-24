---
title: "드림코딩 13: async-await"
date: 2022-12-25
last_modified_at: 2022-12-25
category: study
tags: [JavaScript, 드림코딩, async/await, Promise]
---

# 드림코딩 13: async-await

작성일 2022-12-25
{ .post-date }

promise를 return하면서 promise chaining을 하면 코드가 조금 복잡해질 수도 있기 때문에, async-await을 사용해서 좀 더 간단하게 만들 수 있다.

async와 await은 코드를 동기식으로 작성하는 것처럼, 간단하게 작성할 수 있도록 도와준다. 

async & await : clear style of using promise  
⇒ async와 await을 사용하면, 깔끔한 코드를 작성할 수 있지만, 그렇다고 promise가 나쁘다는 것은 아니다. 항상 async, await을 사용해야만 하는 것은 아니다. 때에 따라 다르다.

---

- async

```javascript
function fetchUser() {
    // do network request... in 10 secs...
    // 백엔드에서 사용자의 데이터를 받아온다.
    return 'ellie';
}

const user = fetchUser();
console.log(user);
```

이와 같은 코드가 있다고 할 때, 자바스크립트는 동기 방식으로 실행되기 때문에, 윗 라인부터 순차적으로 읽어오면서 실행된다. 이 때 fetchUser라는 함수가 백엔드에서 유저의 정보를 가져오는데 10초의 시간이 걸린다고 하면, 자바스크립트 엔진은 해당 함수에서 10초간 대기하다가 데이터가 다 전달되면 가져온 값을 return하게 된다.

이를 방지하기 위해 Promise를 사용할 수 있다.

```javascript
'use strict';

function fetchUser() {
    
    // resolve, reject : executor
    return new Promise((resolve, reject) => {
        //do network request in 10 secs...
        return 'ellie';
    });
}

const user = fetchUser();
console.log(user);
```

그런데, Promise 내부에서 resolve 또는 reject가 실행되지 않으면, Promise는 pending 상태에서 계속해서 대기하게 된다. (pending → fulfilled, reject) 그래서, Promise 안에서 resolve나 reject를 꼭 해주어야 한다.

- return되는 Promise 안에서 resolve를 해준 코드.

```javascript
'use strict';

function fetchUser() {
    
    // resolve, reject : executor
    return new Promise((resolve, reject) => {
        //do network request in 10 secs...
        resolve('ellie');
    });
}

const user = fetchUser();
user.then(console.log);
console.log(user);
```

---

그런데, Promise를 이용하지 않고도, 간편하게 비동기 코드를 작성할 수 있는 방법이 있다.

- async

```javascript
'use strict';

async function fetchUser() {
    // resolve, reject : executor
    return 'ellie';
}

const user = fetchUser();
user.then(console.log);
console.log(user);
```

async 키워드를 function에 붙여주면, 해당 함수가 Promise를 return하게 된다. async라는 키워드를 붙이면, 해당 코드 블럭이 자동으로 Promise로 바뀌게 된다. (Syntactic Sugar)

---

- await

```javascript
function delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

async function getApple() {
    await delay(3000);
    return 'apple';
}

async function getBanana() {
    await delay(3000);
    return 'banana';
}
```

await은 async function 내에서만 사용할 수 있다. delay라는 함수는 Promise를 return하는데, 여기서 await을 쓰게 되면, Promise의 동작이 끝날 때까지 기다려준다. 

async라는 키워드를 function 앞에 붙이게 되면, 해당 함수는 promise를 return하게 되므로, 마치 resolve(return value)와 같이 동작하게 될 것이다.

따라서, 위의 스니펫에서 getApple과 getBanana는 resolve(’apple’), resolve(’banana’) 처럼 동작하게 되는 것이다.

function delay는 timeout을 parameter로 전달받아서, timeout만큼 시간이 지나면, resolve을 수행해주는 Promise를 return한다. (Promise에 resolve, reject가 들어가는데, 꼭 reject까지 넣어주지 않아도 됨?)

⇒ reject를 생략한 구조.

```javascript
function delay(ms) {
    return new Promise((resolve, reject) => setTimeout(resolve, ms));
}
```

위 처럼 reject를 써줘야 하는데, reject를 쓰지 않으니까, 그냥 reject를 생략하고 executor를 만든 것. 그런데, resolve에 인자를 넘겨주지 않아도 되는 모양이다. 

만약, async-await을 쓰지 않고 Promise로 처리하려면, getBanana()는 이렇게 작성해야한다.

```javascript
function getBanana() {
    return delay(3000)
    .then(() => 'banana');
}
```

⇒ 이렇게 작성하는 것보다, 동기적으로 동작하는 것처럼 보이게 작성하는 것이 더 편할 때가 있다.

- await은 왜 붙는가?

```javascript
async function getApple() {
    // await delay(1000);
    return 'apple';
}

async function getBanana() {
    // await delay(1000);
    return 'banana';
}
```

코드에서 await 부분을 comment out 처리하면, 그래도 여전히 Promise가 잘 return된다. 왜냐면, async라는 키워드가 function에 붙어서 자연스럽게 해당 코드 블럭이 Promise로 처리되었기 때문이다. await은 async 내에서 Promise의 동작이 끝나기를 기다려주는 키워드이다.

```javascript
function pickFruits() {
    return getApple() // resolve('apple') : Promise return.
    .then(apple => { // apple : 'apple'
        return getBanana() // resolve('banana') : Promise return.
        .then(banana => `${apple} + ${banana}`); 
        // Promise가 return되었으므로, .then으로 처리.
    });
}

pickFruits().then(console.log);
```

위의 코드처럼 getApple과 getBanana를 사용할 수 있다. 그런데, 이는 마치 이전에 봤던 콜백지옥과 비슷한 형태인 것처럼 보인다. 이를 해결하려면, pickFruits function에도 async를 사용하면 된다.

```javascript
async function pickFruits() {
    const apple = await getApple() // Promise
    // .then(console.log);

    const banana = await getBanana() // Promise
    // .then(console.log);

    // .then으로 Promise를 소진하면, 다시 Promise를 읽을 수 없게 된다.
    // 값이 사라짐. 따라서, .then을 사용하지 않고 아래에서 값을 읽어주면
    // string 값을 return할 수 있게 됨.

    // 이전까지는 Promise를 받아오면 .then으로 무언가 처리를 해주었는데,
    // 바로 처리해주지 않고 받아온 string 값을 사용해도 되는 것으로 보인다.
    return `${apple} + ${banana}`; // 마찬가지로, Promise를 return.
}

// 즉, 여기서 pickFruits는 Promise인데, apple + banana라는 string이 resolve 된 것.
pickFruits().then(console.log);
```

- error 처리하는 법.

```javascript
async function getApple() {
    await delay(1000);
    throw 'error';
    return 'apple'; 
}

async function getBanana() {
    await delay(1000);
    return 'banana';
}

// ...

// error 처리.
async function pickFruits() {
    try {
        const apple = await getApple();
        const banana = await getBanana();
    } catch(e) {
        console.log(e);
    }
}
```

---

그런데, 여기서 문제가 있다. pickFruits()에서, await을 두 개 사용하면 각각에 대해서 다 기다려주어야 하므로, 시간이 오래 걸려 비효율적이다. 이 두 줄의 코드는 서로 연관이 있는 코드가 아니기 때문에, 굳이 동작에 순서를 두지 않는 것이 좋다.

```javascript
async function pickFruits() {
    const applePromise = getApple();
    const bananaPromise = getBanana();

    const apple = await applePromise;
    const banana = await bananaPromise;

    return `${apple} + ${banana}`;
}
```

따라서, 위의 스니펫 처럼 우선 Promise를 바로 실행시켜 버리고, (마치 linux에서 join 처럼…) 나중에 await을 각각 걸어두면, await이 병행적으로 실행된다. 하지만, 이 또한 코드를 너무 복잡하게 작성한 것이다. 자바스크립트 Promise에서 지원하는 API를 사용하면 이를 간단하게 구현할 수 있다.

## Useful Promise APIs

- Promise.all() Promise 배열을 전달하게 되면, 모든 Promise들이 병렬적으로 실행된다.

```javascript
function pickAllFruits() {
    return Promise.all([getApple(), getBanana()])
    .then(fruits => fruits.join(' + '));
}

pickAllFruits().then(console.log);
```

---

- 마지막 예시로, 만약, 종류에 관계없이 가장 먼저 동작되는 것의 결과만 가져온다고 했을 때 코드를 작성한다.

사과를 따는데 2초가 걸리고, 바나나를 따는데 1초가 걸린다고 가정하자.

```javascript
async function getApple() {
    await delay(2000);
    return 'apple'; 
    // getApple을 사용하게 되면, Promise로 'apple'을 return받게 된다.
}

async function getBanana() {
    await delay(1000);
    return 'banana';
    // getBanana를 사용하게 되면, Promise로 'banana'를 return받게 된다.
}
```

```javascript
function pickOnlyOne() {
    return Promise.race([getApple(), getBanana()]);
}

pickOnlyOne().then(console.log);
```

⇒ 마치, linux에서 thread를 다룰 때, race condition 처럼, race API를 사용함.

> 두 가지 이상의 Promise를 사용한다면, .then을 이어서 쓰는 것보다 async-await을 사용하는 것이 더 좋다.

```javascript
async function displayUser() {
	const user = await fetchUser();
	const profile = await fetchPropfile(user);
	updateUI(user, profile);
}
```
