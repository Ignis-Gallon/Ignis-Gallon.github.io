---
title: "Factories - Generic Interface for Creating"
date: 2022-11-18
last_modified_at: 2022-11-19
category: study
tags: [Node.js, 디자인 패턴, Factory, Abstract Factory]
---

# Factories - Generic Interface for Creating

작성일 2022-11-18 · 최종 수정일 2022-11-19
{ .post-date }

## Factory pattern

factory pattern originally defined by the Gang of Four as an interface for creating an object which lets subclasses decide which class to instantiate. This pattern is also known as the virtual constructor pattern. At first glance, this may sound a little confusing; but let’s try to understand, using an analogy of a factory that manufactures mobile phones.

Let’s assume a phone in an elementary form has a model name; a processor type; a defined amount of RAM; dimensions; and screen resolution.

The phone factory can manufacture all kinds of phones, and you typically need to supply them with combinations of these feature attributes to get the job done. Once the company has combinations of specs listed as models A, B, and C, you just have to tell the factory to manufacture a model, and they’ll do it for you. You don’t have to list the spec again and again. In a nutshell, the factory pattern allows you to generate preconfigured custom objects easily without the need to pass in constructor options every time.

---

## Code

- Normal Factory Pattern

팩토리 패턴은 사전에 정의된 객체의 instance를 간단한 방법으로 만들어내는 인터페이스 패턴이다. 이 예제에서는 기초적인 형태의 데이터를 저장하는 Phone class를 활용한다.

Phone factory는 사전에 입력된 모든 종류의 Phone class의 instance를 만들어낼 수 있다. 기기의 종류 A, B, C가 존재한다고 할 때  사용자가 factory를 통해 static한 데이터가 아닌 dynamic한 데이터만 constructor를 통해 넘겨주며 Phone의 instance를 만들어낼 수 있다. 다음 예제를 통해 살펴보자.

- Phone.js : 가장 기초가 되는 Phone Class이다. 이 class를 활용하여 다른 종류의 기기 class를 만들어내도록 한다. 해당 클래스는 전달받은 번호로 전화를 걸어주는 dial(num) method와 현재 디바이스의 설정을 보여주는 displayConfig() method를 갖는다.

```javascript
class Phone {
    constructor(
        serialNum,
        model = 'Generic',
        processor = 'Generic',
        RAM = '2 Gb',
        width = 67.3,
        height = 138.4,
        depth = 7.3,
        resolution = '750 x 1334'
    ) {
        this.serialNum = serialNum;
        this.configuration = {
            model,
            processor,
            RAM,
            width,
            height,
            depth,
            resolution
        }
    }
    dial(num) {
        console.log(`Now dialing ${num}...`);
    }
    displayConfig() {
        console.log(`${JSON.stringify(this.configuration)}`);
    }
}

module.exports = Phone;
```

- iPhoneXR.js

```javascript
const Phone = require('./Phone');

class iPhoneXR {

    // This is known as the factory pattern.
    constructor(serialNum) {
        return new Phone(serialNum, 'iPhone Xr', 'A12 Bionic', '3 Gb', 75.7, 150.9, 8.3, '828 x 1792');
    }
}

// export this class as a module.
module.exports = iPhoneXR;
```

- iPhoneXs.js

```javascript
const Phone = require('./Phone');

class iPhoneXS {

    // This is known as the factory pattern.
    constructor(serialNum) {
        return new Phone(serialNum, 'iPhone Xs', 'A12 Bionic', '4 Gb', 70.9, 143.6, 7.7, '1125 x 2436');
    }
}

// export this class as a module.
module.exports = iPhoneXS;
```

- iPhoneXSMax.js

```javascript
const Phone = require('./Phone');

class iPhoneXSMax {

    // This is known as the factory pattern.
    constructor(serialNum) {
        return new Phone(serialNum, 'iPhone Xs Max', 'A12 Bionic', '4 Gb', 77.4, 157.5, 7.9, '1242 x 2688');
    }
}

// export this class as a module.
module.exports = iPhoneXSMax;
```

→ 위 세 개의 class는 Phone constructor로 dynamic data(각각의 핸드폰 기종 class가 다르게 갖는 데이터인 serial Number)를 전달받아 Phone instance를 전달해주는 역할을 한다. 세 개의 서로 다른 class가 일종의 factory 역할을 하는 것이다.

- app.js

```javascript
// const Phone = require('./Phone');

// const iPhoneXR = new Phone('IPXR-0001', 'iPhone Xr', 'A12 Bionic', '3 Gb', 75.7, 150.9, 8.3, '828 x 1792');

// iPhoneXR.displayConfig();
// iPhoneXR.dial(12345678);

// creating iphone only this way is tedious(지루한)
const iPhoneXR = require('./iPhoneXR.js');
const iPhoneXS = require('./iPhoneXS.js');
const iPhoneXSMax = require('./iPhoneXSMax.js');

const myNewiPhone = new iPhoneXR('XR-0001');
const myNewiPhoneXS = new iPhoneXS('XS-0001');
const myNewiPhoneXSMax = new iPhoneXSMax('XSMax-0001');

myNewiPhone.displayConfig();
myNewiPhone.dial(12345678);

myNewiPhoneXS.displayConfig();
myNewiPhoneXS.dial(12345678);

myNewiPhoneXSMax.displayConfig();
myNewiPhoneXSMax.dial(12345678);
```

그런데, 이와 같은 방법은 상당히 비효율적이라고 볼 수 있다. 왜냐하면, 세 개의 iPhone 기종은 같은 data spec을 표기해야 되기 때문에 Abstract Factory Pattern을 통해 하나의 singleton factory를 만드는 것이 효율적인 방법이라고 볼 수 있다.

---

## Abstract Factory Pattern

- Phone.js와 세 개의 device에 대한 class는 이전과 같음.
- iPhoneFactory.js

```javascript
const iPhoneXR = require('./iPhoneXR');
const iPhoneXS = require('./iPhoneXS');
const iPhoneXSMax = require('./iPhoneXSMax');

class iPhoneFactory {
    create(type, serialNum) {
        switch (type) {
            case 'iPhone Xr':
                return new iPhoneXR(serialNum);
            case 'iPhone Xs':
                return new iPhoneXS(serialNum);
            case 'iPhone Xs Max':
                return new iPhoneXSMax(serialNum);
            default: {
                console.log('Unknown iPhone type...');
            }
        }
    }
}

// singleton으로 factory class를 내보낸다.
// 새롭게 instance를 만들 필요가 없는 것임.
module.exports = new iPhoneFactory();
```

이와 같은 방법을 통해 singleton factory class에 디바이스의 type과 serialNumber를 넣으면 하나의 클래스로 세 개의 디바이스에 대한 instance를 만들어 return해줄 수 있다. 이 때, module의 export는 iPhoneFactory의 instance를 전달해줌으로써 singleton pattern으로 구현되도록 만든다. 왜냐면, 해당 디바이스 class들을 생산하는 factory가 굳이 이 package에서 여러 개 만들어질 필요는 없기 때문이다.

- app.js

```javascript
const iPhoneFactory = require('./iPhoneFactory');

const iPhoneXR = iPhoneFactory.create('iPhone Xr', 'xr-0001');
const iPhoneXS = iPhoneFactory.create('iPhone Xs', 'xs-0002');
const iPhoneXSMax = iPhoneFactory.create('iPhone Xs Max', 'xsmax-0003');

iPhoneXR.displayConfig();
iPhoneXS.displayConfig();
iPhoneXSMax.displayConfig();
```

iPhoneFactory에 대한 require를 선언한 후, 각각의 디바이스 기종에 대하여 instance를 생성한 뒤, instance에 선언된 method를 사용해 정상적으로 instance가 생성된지 확인한다.

---

## Summarize

- The factory pattern allows you to offer an easy-to-use for building preconfigured objects. 
- This pattern allows you to write cleaner code that saves you from errors, while instantiating base constructors with a host of arguments.
- The factory pattern is particularly useful when writing npm packages for public use. 
- By hiding away the meat of the implementation and offering an easy-to-use interface, you can make the package easier to use.
- The public interface can be made even better by implementing the abstract factory pattern, which offers an even cleaner interface for building a family of related objects.
- For instance, we created an iPhone factory class that allows us to easily create instances of three separate models of the iPhone.
