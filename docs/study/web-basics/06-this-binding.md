---
title: "this 바인딩"
date: 2023-02-03
last_modified_at: 2023-02-04
category: study
tags: [JavaScript, this, 바인딩, 화살표 함수]
---

# this 바인딩

작성일 2023-02-03 · 최종 수정일 2023-02-04
{ .post-date }

JS에서 this는 기본적으로 window 를 의미한다. 전역적으로 호출되거나, 함수 내에서 호출되면 this는 window를 가리킨다. 하지만, 어떤 객체(object) 안에서 this를 사용하게 되면 this는 그 객체를 가리키게 된다.

그런데 만약 객체 안에서 선언한 메서드의 함수에서 this를 쓰면 어떻게 될까? 결과적으로 this는 window를 가리킨다. 이는 단순 함수 호출 규칙에 따라 window를 가리키게 된다.

- call(), apply(), bind()

JS에서는 각기 다른 문맥의 this를 필요에 따라 변경할 수 있도록 함수를 제공한다. call(), apply(), bind() 등이 있다.

```javascript
// 1. 이것은 객체의 메소드
var man = {
    name: 'john',
    // 2. 객체의 메소드 안에서 함수를 선언하는 것이니까 내부 함수
    hello: function() {
        function getName() {
            // 3. 여기서 this가 무엇을 가리키고 있을까? : window이다.
            return this.name;
        }
        // 4. 이번에는 call()을 통해 현재 문맥에서의 this(man 객체)를 바인딩해주었다
        console.log('hello ' + getName.call(this));
    }
}

// 이번에는 메소드를 실행시키면 john가 뜬다!
// this가 man 객체로 바인딩 된 것을 확인할 수 있다
man.hello();
```

- call() : call을 이용하면 함수에서 사용되는 this를 객체로 binding 해줄 수 있다.
- callback 함수에서도 this는 window를 가리킨다.

```javascript
// 1. 콜백함수
var object = {
    callback: function() {
        setTimeout(function() {
            console.log(this); // 2. this는 window
        }, 1000);
    }
}
```

- 생성자

함수를 new 키워드로 생성할 때, this를 해당 객체에 바인딩 할 수 있다.

```javascript
// 1. 클래스 역할을 할 함수 선언
function Man () {
    this.name = 'John';
}

// 2. 생성자로 객체 선언
var john = new Man();

// 3. this가 Man 객체를 가리키고 있어 이름이 정상적으로 출력된다
john.name; // => 'John'
```

- 클래스

ES6에 추가된 문법인 클래스로도 this 바인딩이 가능하다.

```javascript
// 1. Class Man 선언
class Man {
    constructor(name) {
        this.name = name;
    }
    hello() {
        console.log('hello ' + this.name)
    }
}

// 2. 생성자 실행
var john = new Man('John');
john.hello(); // 3. hello John 출력
```

- 화살표 함수의 this

JavaScript 코드를 실행하기 위한 변수, 함수 등의 정보를 담고 있는 환경을 컨텍스트라고 간단하게 생각해볼 수 있다.

정적 컨텍스트는 함수가 실행된 위치가 아닌, 정의된 위치에서의 컨텍스트를 참조한다. 이 코드가 실행되고 그런 것을 따질 필요 없이 그냥 정의된 부분에서 가장 가까운 this만 보면 되는 것이다.

화살표 함수는 이와 같은 정적 컨텍스트가 사용된다.

```javascript
// 1. 화살표 함수
var obj = {
    a: this, // 2. 일반적인 경우 this는 window,
    b: function() {
      console.log(this) // 3. 메소드의 경우 this는 객체
    },
    c: () => {
        console.log(this)
        // 4. 화살표 함수의 경우 정적 컨텍스트를 가짐, 함수를 호출하는 것과 this는 연관이 없음
        // 5. 따라서 화살표 함수가 정의된 obj 객체의 this를 바인딩하므로 this는 window
    }

}

obj.b() // 6. obj
obj.c() // 7. window
```

일반적인 방법으로 함수를 선언하면, 일반적인 함수는 함수가 실행될 때 자체적으로 this를 할당하게 되는데, 이 함수는 메소드 함수이므로 this가 메소드를 포함하는 객체로 바인딩된다. (function () {…})

하지만 화살표 함수는 this가 없기 때문에, 부모 스코프의 this를 바인딩하는데, 위의 예시에서 this는 곧 window 객체를 의미한다. 따라서, 메소드로 화살표 함수를 쓰면, this를 이용한 부모 객체를 접근할 수 없다.

[JavaScript 개발자라면 꼭 알아야 할 this - 재그지그의 개발 블로그](https://wormwlrm.github.io/2019/03/04/You-should-know-JavaScript-this.html.html)

---

this는 C++에서는 class 객체 자신을 가리키는 용도로 사용한다. 하지만, javascript에서는 상당히 다른 의미로 사용된다.

this는 기본적으로 window를 의미하고, 어떠한 객체 안에서 this는 그 객체를 의미하게 된다. 또는 binding을 통해서 this를 지정해줄 수도 있다. 함수 안에서는 기본적으로 this === window이지만, 어떠한 객체 안에서 정의된 함수라면 this === object이다. 그런데, 어떠한 객체 안에서 정의된 함수가 있고, 그 안에서 정의된 추가적인 함수에서 this를 쓴다면 그 this === window이다. 기본 함수 스코프가 적용되기 때문이다.
