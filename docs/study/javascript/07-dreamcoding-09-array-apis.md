---
title: "드림코딩 9: array APIs"
date: 2022-12-24
last_modified_at: 2022-12-25
category: study
tags: [JavaScript, 드림코딩, 배열, 배열 API]
---

# 드림코딩 9: array APIs

작성일 2022-12-24 · 최종 수정일 2022-12-25
{ .post-date }

```javascript
// Q1. make a string out of an array

{
    const fruits = ['apple', 'banana', 'orange'];
    let str = "";

    for (let fruit of fruits) {
        str += fruit;
        str += " ";
    }

    console.log(str);

    // 이렇게 할 수도 있지만, 쉽게 .join()이라는 api사용 가능.
    const result = fruits.join();
    console.log(result);

    const result2 = fruits.join(" ");
    console.log(result2);
}
```

join(separator?: string): string;

join은 배열의 내용을 string으로 만들어서 반환해주는 array API이다. separator는 optional하므로, 주지 않아도 된다. 그런데 separator를 주게 된다면, 해당 구분자를 바탕으로 array의 원소들을 구분해서 string으로 만들어 반환하게 된다. (구분자를 넣지 않으면 ,가 default value임)

```javascript
// Q2. make an array out of a string
{
    console.clear();
    const fruits = 'apple, kiwi, banana, cherry';

    const fruits_array = new Array();
    let str = "";

    for (let character of fruits) {
        
        if (character === ',') {
            fruits_array.push(str);
            str = "";
            continue;
        }
        else if (character === ' ') {
            continue;
        }

        str += character;
    }
    fruits_array.push(str);
    str = "";

    console.log(fruits_array);

    // 이렇게 할 수도 있지만, split 사용 가능. (문자열의 API)
    console.log(fruits_array);
    console.clear();

    const result = fruits.split(', ');
    console.log(result);
}
```

split(separator: string | RegExp, limit?: number): string[];

split은 string의 API이다. 특정한 구분자를 지정해주면 그 구분자를 기준으로 string을 자르고, string의 array로 반환한다. RegExp으로 사용할 수도 있다. limit?라는 optional한 parameter가 있는데, 이를 이용하면 앞에서부터 몇 개의 string을 반환받을지 결정할 수 있다.

```javascript
// Q3. make this array look like this: [5, 4, 3, 2, 1];
{
    const array = [1, 2, 3, 4, 5];
    const result = array.reverse();
    console.log(result);
    console.log(array);
}
```

array.reverse() : 배열의 요소를 뒤집고, 뒤집은 배열을 반환한다. 이렇게 되면 배열의 원본 자체가 뒤집힌다.

```javascript
// Q4. make new array without the first two elements
{
    console.clear();

    const array = [1, 2, 3, 4, 5];
    const new_array = array.splice(2, array.length-2);
    console.log(new_array);
}
```

```javascript
// Q5. find a student with the score 90
{
    console.clear();

    let stu;

    for (let i = 0; i < students.length; i++) {
        if (students[i].score === 90) {
            stu = students[i];
            break;
        }
    }

    console.log(stu);
    console.clear();

    // find API를 이용하면, 쉽게 찾을 수 있다.
    const result = students.find((student, index) => {
        console.log(student, index);
    })
    // 임시적으로 find의 기능을 확인하기 위해 찍어봄. 다섯 명의 student가 출력됨.
    const result2 = students.find((student) => student.score === 90);
    console.log(result2);
}
```

```typescript
find<S extends T>(predicate: (this: void, value: T, index: number, obj: T[]) => value is S, thisArg?: any): S | undefined;
find(predicate: (value: T, index: number, obj: T[]) => unknown, thisArg?: any): T | undefined;

    /**
     * Returns the index of the first element in the array where predicate is true, and -1
     * otherwise.
     * @param predicate find calls predicate once for each element of the array, in ascending
     * order, until it finds one where predicate returns true. If such an element is found,
     * findIndex immediately returns that element index. Otherwise, findIndex returns -1.
     * @param thisArg If provided, it will be used as the this value for each invocation of
     * predicate. If it is not provided, undefined is used instead.
     */
```

find는 this, value, index, obj 총 네 개의 인자를 받는 함수가 전달되고, bool값을 return하는 함수를 callback function으로 전달해주면 된다. (value is S ⇒ boolean)

만약, 주어진 조건에 맞는 element가 찾아지면, 바로 true를 return한다. 만약, 조건을 만족하는 element가 없다면 -1을 return한다.

우리가 지정한 callback 함수는 배열에 있는 모든 요소들마다 실행이 되며, 배열에 들어있는 아이템들마다 하나하나씩 호출하게 되고, 특정 원소가 true를 return하게 되면, iteration을 바로 멈추게 된다.

```javascript
// Q.6 make an array of enrolled students
{
    const result = students.filter((student) => student.enrolled);
    console.log(result);
}
```

```javascript
// Q.7 make an array containing only the students' scores 
{
    console.clear();

    const result = students.map((student) => student.score);
    console.log(result);

    const result2 = students.map((student) => student.score * 2);
    console.log(result2);
}
```

map : 배열 안에 들어있는 요소 각각을, 우리가 원하는 함수를 거쳐서 값을 보정해 다른 데이터로 만들어준다. map을 이용하면 새로운 배열을 return할 수 있다.

```javascript
// Q.8 check if there is a student with the score lower than 50
{
    console.clear();
    const result = students.some((student) => student.score < 50);
    console.log(result);

    const result2 = !students.every((student) => student.score >= 50);
    console.log(result2);
}
```

`some` : 배열에서, 하나라도 주어진 조건에 만족되는 원소가 있다면, true를 return하고, 없다면 false를 return시킨다.

동일하게, every를 사용할 수 있다.

`every` : 배열에 있는 모든 요소들이 주어진 조건을 만족해야만 true를 return하게 된다.

```javascript
const result = students.reduce((prev, curr) => {
        console.log('--------------');
        console.log(prev);
        console.log(curr);
        return prev + curr.score;
    }, 0); // initial value 0
    console.log(result/(students.length-1));

    const result2 = students.reduceRight((prev, curr) => {
        console.log('--------------');
        console.log(prev);
        console.log(curr);
        return prev + curr.score;
    }, 0) // initial value 0
    console.log(result2/(students.length-1));
```

```typescript
reduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T): T;
reduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[]) => T, initialValue: T): T;
    /**
     * Calls the specified callback function for all the elements in an array. The return value of the callback function is the accumulated result, and is provided as an argument in the next call to the callback function.
     * @param callbackfn A function that accepts up to four arguments. The reduce method calls the callbackfn function one time for each element in the array.
     * @param initialValue If initialValue is specified, it is used as the initial value to start the accumulation. The first call to the callbackfn function provides this value as an argument instead of an array value.
     */
```

reduce : callback 함수는 배열 안에 들어있는 모든 요소에 대하여 실행된다. 그런데, callback 함수에서 return되는 값은, 그동안 모든 요소를 탐색하면서 함께 누적된 값이 return된다. (static 변수 이용)

위의 예시를 보면, curr로 return하는 값이, 다음 번 iteration의 prev로 동작한다. reduceRight은 반복을 뒤에서 부터 실행한다.

```javascript
// Q10. make a string containing all the scores
// result should be : '45, 80, 90, 66, 88';
{
    const result = students
    .map((student) => student.score)
    .filter((score) => score >= 50)
    .join()

    console.log(result);

    // sorting...
    const result2 = students
    .map((student) => student.score)
    .sort((a, b) => a - b);
    // .sort((a, b) => b - a);
    console.log(result2);
}
```

map은 새로운 배열을 return한다. 그런데, return하는 값이 새로운 array이기 때문에 API들을 엮어서 사용할 수 있다.

이를 함수형 프로그래밍이라고 한다.
