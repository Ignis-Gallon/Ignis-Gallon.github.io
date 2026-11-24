---
title: "드림코딩 6: class, object"
date: 2022-12-22
last_modified_at: 2022-12-24
category: study
tags: [JavaScript, 드림코딩, class, 상속]
---

# 드림코딩 6: class, object

작성일 2022-12-22 · 최종 수정일 2022-12-24
{ .post-date }

자바스크립트에는 ES6에 class가 도입되었다. 이전까지는 클래스를 정의하지 않고 바로 object를 만들 수 있었다. 여기서는 class를 이용하는 법을 다룬다.

즉, 자바스크립트의 클래스는 기존에 존재하던 것 위에 추가된 것으로, 완벽하게 추가된 것이 아니라 프로토타입을 베이스로한 것을 기반으로 만들어진 것이다. (간편하게 쓸 수 있도록, 문법만 클래스가 추가된 것) ⇒ syntactical sugar

- class의 선언

```javascript
class Person {

    // fields, members...
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    // methods
    speak() {
        console.log(`${this.name}: hello!`);
    }
}
```

새로운 object를 만들 때는, new라는 키워드를 사용한다.

```javascript
// 새로운 오브젝트의 생성
const ellie = new Person('ellie', 20);
```

---

- getter, setter

```javascript
class User {
    
    constructor(firstName, lastName, age) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }

    // getter, setter
    get age() {
        return this.age;
    }
    set age(value) { 
        this.age = value;
    }
}
```

그런데, 위의 코드처럼 작성하면 문제가 발생한다. 우리가 getter를 정의하는 순간, constructor에 있는 this.age는 메모리에 있는 값을 읽어오는 것이 아니라 getter를 호출하게 된다. 그리고 setter를 정의하는 순간, constructor에서 this.age = age; 로 값을 할당하는 순간 메모리에 바로 값을 할당하는 것이 아니라, setter를 호출하게 된다. 이렇게 되면 setter안에서는 다시 getter를 호출하게되고, getter에서는 다시 setter를 호출하게 되고… 이런 식으로 계속 반복되다보니까 call stack size exceeded error가 발생하게 된다. 

이를 방지하기 위해 getter와 setter 안에서는 변수의 이름을 다르게 설정해주어야 한다.

```javascript
class User {
    
    constructor(firstName, lastName, age) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }

    // getter, setter
    get age() {
        return this._age;
    }
    set age(value) { 
        this._age = value < 0 ? 0 : value;
    }
} 

const user1 = new User('Steve', 'Jobs', -1);
console.log(user1.age);
```

이 상태에서 우리가 객체에서 user1.age를 출력할 수 있는 것은, getter와 setter가 있기 때문이다. 실제 field의 이름은 _age이지만, getter, setter를 통해 접근할 수 있게 되는 것이다.

---

- public vs private

```javascript
class Experiment {
    publicField = 2;
    #privateField = 0;
}

const experiment = new Experiment();
console.log(experiment.publicField);
console.log(experiment.privateField);
```

생성자를 사용하지 않고 field를 선언하게 되면 public, 변수의 앞에 #을 붙이면 private이 된다. 실제로 출력을 해보면, privateField는 undefined가 나온다.

---

- static

object, data에 관계없이 class가 갖고 있는 고유한 값을 동일하게 반복적으로 사용할려고 한다면, field 또는 method를 static으로 선언한다. 이는 class와 연결되어 있다.

```javascript
class Article {
    static publisher = 'Dream Coding';

    constructor (articleNumber) {
        this.articleNumber = articleNumber;
    }

    static printPublisher() {
        console.log(Article.publisher);
    }
}

const article1 = new Article(1);
const article2 = new Article(2);
console.log(article1.publisher);
// 이렇게 출력하면, undefined가 나오게 된다.
// 그 이유는 Article에 선언된 publisher field는 static이기 때문에
// 객체로서 호출할 수 없다.
// 따라서, 호출하려면 class의 이름으로 호출해줘야 한다.

console.log(Article.publisher);
Article.printPublisher();
```

Typescript에서도 유용하게 사용된다. 클래스에서 공통적으로 사용하는 field라면, static을 이용하는 것이 메모리 측면에서 유용하다.

---

- 상속과 다형성

```javascript
class Shape {
    constructor(width, height, color) {
        this.width = width;
        this.height = height;
        this.color = color;
    }

    draw() {
        console.log(`drawing ${this.color} color of`);
    }

    getArea() {
        return this.width * this.height;
    }
}

// 아래와 같이 extends keyword를 이용하여 Shape을 상속받을 수 있다. {} <= 써줌.
// 그리고, block 안에서는 method overriding을 할 수 있다. (polymorphism)
class Rectangle extends Shape {}
class Triangle extends Shape {
    // overriding
    draw() {
        super.draw(); // 이와 같이 부모 클래스의 method를 호출할 수도 있음. (superclass)
        console.log('ㅅ');
    }
    getArea() {
        return (this.width * this.height) / 2;
    }
}

const rectangle = new Rectangle(20, 20, 'blue');
rectangle.draw();

console.log(rectangle.getArea());

const triangle = new Triangle(20, 20, 'red');
triangle.draw();
console.log(triangle.getArea());
```

---

- instanceof

왼쪽에 있는 object과 오른쪽에 있는 class의 instance인지 아닌지 확인하는 함수이다.

```javascript
console.log(rectangle instanceof Rectangle); // true
console.log(triangle instanceof Rectangle); // false
console.log(triangle instanceof Triangle); // true
console.log(triangle instanceof Shape); // true
console.log(triangle instanceof Object); // true
```

자바스크립트에서 클래스는 Object의 instance로 취급한다. 즉, 모든 Class는 Object를 상속받아서 구현된다. 그러므로 Object의 method들을 모든 Class에서도 사용 가능하다. (ex. toString()…)

---

- javascript mdn references

자바스크립트의 object는 어떤 것들이 있는지 확인 가능하다.
