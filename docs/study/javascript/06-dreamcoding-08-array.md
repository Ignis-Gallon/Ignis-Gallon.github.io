---
title: "드림코딩 8: array"
date: 2022-12-24
last_modified_at: 2022-12-24
category: study
tags: [JavaScript, 드림코딩, 배열]
---

# 드림코딩 8: array

작성일 2022-12-24
{ .post-date }

```javascript
// array declaration
const arr1 = new Array();
const arr2 = [1, 2];
```

```javascript
// Index Position
const fruits = ['apple', 'banana']; // 배열의 선언

// 배열의 출력
console.log(fruits);
console.log(fruits.length);
console.log(fruits[0]);
console.log(fruits[1]);
console.log(fruits[2]); // undefined
console.log(fruits.length - 1); // 제일 마지막 요소
```

---

- Looping

```javascript
console.log("기본적인 방법")
for (let i = 0; i < fruits.length; i++) {
    console.log(fruits[i]);
}

console.log("for of");
for (let fruit of fruits) {
    console.log(fruit);
}

console.log("forEach");
// 콜백 함수를 받아온다.
fruits.forEach((value) => {
    console.log(value);
})

// 보통 array까지는 받아오지는 않는다. 
// 여기서는 예를 들기 위해 써줌.
fruits.forEach((fruit, index, array) => {
    console.log(fruit, index, array);
})
```

forEach는 자바스크립트에서 제공되는 array API이다. 선언된 곳의 시그니쳐와 코멘트는 다음과 같다.

```typescript
forEach(callbackfn: (value: T, index: number, array: T[]) => void, thisArg?: any): void;
    /**
     * Calls a defined callback function on each element of an array, and returns an array that contains the results.
     * @param callbackfn A function that accepts up to three arguments. The map method calls the callbackfn function one time for each element in the array.
     * @param thisArg An object to which the this keyword can refer in the callbackfn function. If thisArg is omitted, undefined is used as the this value.
     */
```

forEach의 API 설명을 보면, 총 두 개의 인자를 받는 것을 확인할 수 있다. 첫 번째 인자는 callback 함수이다. callback 함수는 총 세 개의 인자를 받을 수 있는데, 첫 번째는 배열 내의 값. 두 번째는 index. 세 번째는 그 배열 자체이다. 일반적으로 value와 index는 자주 사용하지만, array 자체는 거의 받아오지는 않는다. 그리고 forEach의 두 번째 인자로 thisArg?가 있는데, ?가 붙어있다면 줘도 되고 안줘도 된다는 뜻이다.

forEach는 anonymous function을 사용하는 경우가 많으므로, arrow function을 이용한다.

---

- Addition, deletion, copy

```javascript
fruits.push('strawberries', 'peach');
console.log(fruits);

fruits.pop(); // 뒤에서 제거됨.
console.log(fruits);

fruits.unshift('lemon', 'grape');
console.log(fruits);

fruits.shift();
console.log(fruits);
```

- push : 배열의 뒤에다가 값을 추가한다.
- pop : 배열의 뒤에서 값을 제거한다.
- unshift : 배열의 앞에다가 값을 추가한다. (뒤로 밀면서 값을 앞으로 넣어주는 느낌)
- shift : 배열의 앞에서부터 값을 제거한다. (뒤에서 앞으로 밀면서 하나를 밀어주는 느낌)

> shift, unshift 는 pop과 push보다 매우 느리다. 왜냐면, 배열의 모든 값을 한 번씩 옮겨주어야 하기 때문이다. 그러므로 배열의 길이가 길면 길수록 shift의 수행 시간이 매우 길어진다. 

- splice(지우기 시작할 index, 몇 개나 지울 것인지) : splice는 배열에서 특정 index의 원소를 제거할 수 있다. optional하게 몇 개나 지울 것인지 두 번째 인자로 전달해줄 수 있는데, 이를 전달해주지 않으면 시작 index를 기준으로 뒤에 오는 모든 원소를 제거하므로, 적절하게 전달해주어서 사용해야 한다.

```javascript
// remove an item by index position
fruits.splice(1);
fruits.splice(1, 2);
fruits.splice(1, 1, 'watermelon', 'melon'); // 특정 지점을 지우고, 원소 삽입 가능
```

- combine two arrays into one array

```javascript
// combine two arrays into one array
const fruits2 = ['pear', 'coconut'];
const newFruits = fruits.concat(fruits2);

console.log(newFruits);
```

concat(concatenate)을 이용해서 두 개의 배열을 이어붙일 수 있다.

- searching

```javascript
console.log(fruits);
console.log(fruits.indexOf('grape')); // 특정 값의 index

console.log(fruits.indexOf('watermelon'));
console.log(fruits.includes('watermelon'));

console.log(fruits.includes('none')); // 없을 경우 false 반환.
console.log(fruits.indexOf('none')); // 없을 경우 -1 반환.

fruits.push('grape');
console.log(fruits);
console.log(fruits.indexOf('grape')); // 첫 번째 index를 출력.
console.log(fruits.lastIndexOf('grape')); // 마지막 index를 출력.
```
