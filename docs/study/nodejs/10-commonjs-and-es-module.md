---
title: "commonJS & ES module"
date: 2022-11-18
last_modified_at: 2022-11-25
category: study
tags: [Node.js, CommonJS, ES module, 모듈 시스템]
---

# commonJS & ES module

[https://blog.logrocket.com/commonjs-vs-es-modules-node-js/](https://blog.logrocket.com/commonjs-vs-es-modules-node-js/)

browser Javascript 생태계에서 자바스크립트 모듈은 `import`, `export` 구문을 사용하며, 이 구문들은 ECMAScript 모듈 (또는 ES modules)을 각각 불러오거나 내보낸다.

ES module 포맷은  자바스크립트 코드의 재활용을 위한 공식 스탠다드 포맷이며, 대부분의 모던 웹 브라우저들은 모듈 기능을 지원한다.

하지만 Node.js는 default 포맷으로 CommonJS를 지원한다. CommonJS는 module을 불러오기 위해 `require()`를 사용하고 CommonJS module로 내보내지는 변수나 함수는 `module.exports`를 통해 나가게 된다. 

이전에 Node.js 버전이 낮을 때는 ES module이 실험적으로 지원되었지만, 이제 버전이 업데이트되면서 ES module이 안정화 되었다. 이 페이지는 commonJS와 ES module의 주 사용법에 대해서는 다루지 않으며, 두 모듈 방식의 차이점에 대해 집중적으로 설명한다.

---

## Comparing CommonJS modules and ES modules syntax

기본적으로 Node.js는 CommonJS module을 사용하여 Javascript code를 취급한다. CommonJS module은 `require()` 구문을 통해 모듈을 import하고, `module.exports` 구문을 통해 module을 export한다.

예를 들어, 아래의 예제는 두 함수를 CommonJS에서 export하는 방법을 다룬다.

```javascript
module.exports.add = function(a, b) {
        return a + b;
} 

module.exports.subtract = function(a, b) {
        return a - b;
}
```

아래의 방법을 이용하면, CommonJS로써 모듈을 import할 수 있다.

```javascript
const {add, subtract} = require('./util')

console.log(add(5, 5)) // 10
console.log(subtract(10, 5)) // 5
```

---

반면에, 라이브러리를 사용할 때 단순히 Node.js 패키지의 확장자를 `.js`에서 `.mjs`로 바꾸는 것만으로 ES module을 사용할 수도 있다.

```javascript
// util.mjs

export function add(a, b) {
        return a + b;
}

export function subtract(a, b) {
        return a - b;
}
```

아래의 방법을 통해 모듈을 import할 수 있다.

```javascript
// app.mjs

import {add, subtract} from './util.mjs'

console.log(add(5, 5)) // 10
console.log(subtract(10, 5)) // 5
```

ES module을 사용하는 또 다른 방법은, package.json file에 `type:module`이라는 field를 추가하는 것이다.

```javascript
{
  "name": "my-library",
  "version": "1.0.0",
  "type": "module",
  // ...
}
```

package.json file을 위와 같이 수정하면, Node.js는 해당 package 안에 있는 모든 파일을 ES module로써 취급하며, 파일 확장자를 .mjs로 바꾸지 않아도 된다.

> 대안적으로, transpiler를 사용하여 ES module 문법을 CommonJS 문법으로 바꾸어 사용할 수도 있다. (Babel이라는 transpiler가 존재함.) 관심 있는 사람은 아래 링크를 참고…  
>   
> [https://blog.logrocket.com/babel-vs-typescript/](https://blog.logrocket.com/babel-vs-typescript/)

---

## Pros and Cons of using ES modules and CommonJS modules in Node.js

- ES modules are the standard for JavaScript, while CommonJS is the default in Node.js

ES module 포맷은 JavaScript 모듈 시스템을 표준화 하기 위하여 만들어졌으며, JavaScript 코드의 재사용을 위해 encapsulating을 하는 표준 포맷의 역할을 한다.

반면에 CommonJS 모듈 시스템은 Node.js 자체에 내장되어 있으며, Node.js에서의 ES module 도입에 앞서서, CommonJS는 Node.js의 표준 포맷이 되었다. 결과적으로, Node.js의 수많은 라이브러리들이 CommonJS를 사용해 만들어졌다.

대부분의 영향력있는 브라우저들은 ES module 문법을 지원하며, 당신은 프레임워크 React와 Vue.js처럼 `import` / `export` 를 사용할 수 있다. 이 프레임워크들은 (위에서 언급한) transpiler ‘Babel’을 사용하여 `import` / `export` 문법을 Node.js의 구버전이 지원하는 `require()`로 교체한다.

ES module이 JavaScript의 표준 포맷인 것 외에도, ES module 문법은 `require()`와 비교했을 때 매우 가독성이 높다. ES module을 사용하면 동일한 문법을 사용하기 때문에, JavaScript를 주로 사용하는 웹 개발자들은 Node.js 모듈을 사용하면 이슈를 매우 적게 만나게 될 것이다.

---

## 구버전의 Node.js는 ES module을 지원하지 않는다.

ES module이 JavaScript의 표준 모듈 포맷이 되었지만, 개발자들은 구버전의 Node.js는 ES module을 지원하지 않는다는 사실을 고려해야 한다. (특히 Node.js v9이나 그 아래의 버전들.)

다시 말하자면, ES module을 사용하면, 구버전의 Node.js와는 맞지 않을 수 있다는 것이다. (즉, 이럴 경우에는 `require()`, CommonJS를 사용해야 한다.)

하지만, 새로운 조건적인 exports를 사용하면 dual-mode libraries가 지원된다. 이것은 ES module의 새로운 버전에서 나온 기능으로, 구버전에서 사용되는 CommonJS도 지원이 된다.

요컨대, `import`와 `require()`를 동시에 사용해서 호환성 문제를 해결할 수 있는 것이다.

다음 프로젝트를 확인해보자.

```text
my-node-library
├── lib/
│   ├── browser-lib.js (iife format)
│   ├── module-a.js  (commonjs format)
│   ├── module-a.mjs  (es6 module format)
│   └── private/
│       ├── module-b.js
│       └── module-b.mjs
├── package.json
└── …
```

`package.json` 안에서 우리는 `exports` field를 만들어서 `module-a`를 두 개의 방법을 통해 public module로 `exports`할 수 있다. 반면에, `module-b`는 private module로 제한할 수 있다.

```text
// package.json
{
  "name": "my-library",
  "exports": {
    ".": {
        "browser": {
          "default": "./lib/browser-module.js"
        }
    },
    "module-a": {
        "import": "./lib/module-a.mjs"
        "require": "./lib/module-a.js"
    }
  }
}
```

위에서 제시된 내용을 통해, 우리는 my-library package를 다음과 같은 방식으로 사용할 수 있다.

```javascript
// For CommonJS
const moduleA = require('my-library/module-a')

// For ES6 Module
import moduleA from 'my-library/module-a'

// This will not work
const moduleA = require('my-library/lib/module-a')
import moduleA from 'my-awesome-lib/lib/public-module-a'
const moduleB = require('my-library/private/module-b')
import moduleB from 'my-library/private/module-b'
```

exports field 안에 명시된 경로로 인해, 우리는 public module들을 구체적인 절대 경로의 명시를 하지 않고 `import` ( and `require()` )를 통해 가져올 수 있다.

`.js`와 `.mjs` 경로를 추가해줌으로써 우리는 호환성 문제를 해결해줄 수 있다. 우리는 브라우저와 Node.js 같이 환경이 다른 package 모듈들을 묶어줄 수 있다.

---

## Newer Node.js versions fully support ES modules

Node.js에서 모듈은 ES module로써 취급하려면 반드시 다음의 사항을 고려해야 한다. (다음 사항 중 적어도 한 개는 시행되어야 한다.)

1. 확장자를 .js (for CommonJS) → .mjs (for ES modules) 로 바꾼다.
2. package.json file에서 `{"type": "module"}` field를 추가한다.  
    → 이렇게 하면, 현재 package에 있는 모든 code는 ES module로써 처리될 것이며, `import` / `export` 구문은 require()의 대체로써 사용된다.

---

## CommonJS offers flexibility with module imports

ES module에서 `import` 구문은 반드시 파일의 시작 부분에서만 선언될 수 있다. 만약 다른 곳에서 `import`를 부르면 해당 선언부를 파일의 시작부분으로 옮겨버리거나 error를 throw한다.

반면에, `require()`를 사용하면 runtime에 이미 처리가 된다. 결과적으로 `require()`를 사용하면 코드의 어디서든 선언할 수 있다. 따라서, if 문 등을 이용해 동적으로나 조건적으로 `require()`를 통해 모듈을 불러올 지 말지를 결정할 수 있는 것이다.

예를 들어, 다음 예제를 보면 if 문 안에서 `require()`를 불러오는 것을 확인할 수 있다.

```javascript
if(user.length > 0){
   const userDetails = require(‘./userDetails.js’);
  // Do something ..
}
```

위 예제에서는 userDetails module을 user.length가 0보다 클 경우 불러오게 된다.

---

## CommonJS loads modules synchronously, ES modules are asynchronous

`require()`의 제한점 중 하나는 모듈을 synchronously(동기적) 으로 불러온다는 것이다. 이것은 모듈의 loading processing이 차례차례 이루어진다는 것이다.

쉽게 예측할 수 있듯이, 이것은 모듈이 수 백개 존재하는 큰 규모의 application에서는 일부 성능 저하를 야기할 수 있다. 예컨대, `import`는 asynchronous하게 동작하기 때문에 import보다 성능 면에서 유리할 수 있다.

하지만, 모듈을 적게 사용하는 작은 규모의 application에서는 `require()`의 동기적인 특성이 큰 문제를 일으키지 않는다.

---

## Conclusion: CommonJS or ES modules?

여전히 구버젼의 Node.js를 사용하는 개발자들에게 새로운 ES module을 사용하는 것은 비실용적일 수 있다.

이미 만들어진 프로젝트를 ES module로 교체하는 것은 해당 application이 CommonJS만 지원하는 이전 버젼의 Node.js와 호환되지 않도록 만들 수 있다.

그러므로, 당신의 프로젝트를 ES module로 교체하는 것은 부분적으로 불필요할 수 있다.

새롭게 시작하는 사람의 경우에는, ES module이 현재 JavaScript와 Node.js의 표준 모듈 포맷으로 자리잡고 있기 때문에 ES module을 배우는 것이 유리할 수 있다.

만약 Node.js 프로젝트를 새롭게 시작한다면, ES module은 CommonJS의 대안책을 제시한다. ES module을 사용하면 브라우저와 서버에서 사용되는 JavaScript를 좀 더 쉽게 사용할 수 있게 될 것이다.
