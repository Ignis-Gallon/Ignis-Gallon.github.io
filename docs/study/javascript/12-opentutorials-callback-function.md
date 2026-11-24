---
title: "생활코딩: callback function"
date: 2023-01-26
last_modified_at: 2023-01-26
category: study
tags: [JavaScript, 생활코딩, 콜백, 일급 객체]
---

# 생활코딩: callback function

작성일 2023-01-26
{ .post-date }

## first citizen class

변수의 값이 될 수 있는 것으로, javascript에서는 일반적인 값 뿐만 아니라 function도 일급 객체로서 취급된다. 따라서, 함수를 변수의 값으로 지정할 수 있다.

```javascript
function fn() {
	val = function () {
		return 0;
	}
	
	return val;
}
```

⇒ val을 일급 객체로서 취급한다.

```javascript
val = function() {
	return 0;
}

fn(val);
```

⇒ 함수가 다른 함수의 입력값이 될 수 있다면 그 언어에서는 함수를 일급 객체로서 취급하는 것이다. 따라서 javascript는 함수를 일급 객체로 취급한다.

```javascript
val = function() {
	return 0;
}

function fn(arg) {
	arg();
}

fn(val);
```

⇒ 여기서 val이라는 function은 선언은 되어있지만 실행되지 않고 있다가 fn이라는 함수의 입력 값으로 들어가서 사용되게 된다. 이처럼 바로 실행되지는 않지만 다른 함수에 입력값으로 전달돼서, 다른 함수에 의해서 나중에 호출된다는 함수를 CALLBACK FUNCTION이라고 한다.

---

## callback function

```javascript
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

function callback(element) {
	if (element.length > 6) return true;

	return false;
}

const newWords = words.filter(callback);

console.log(newWords);
```

⇒ 이렇게 만들어도 되지만, 좀 더 간소화하기 위하여 아래처럼 만들 수도 있다.

- 익명 함수 이용

```javascript
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const newWords = words.filter(function(element) {
	return element.length > 6;
});

console.log(newWords);
```

- arrow function 이용

```javascript
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const newWords = words.filter((element) => {
	return element.length > 6;
});

console.log(newWords); 
```

```javascript
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const newWords = words.filter(element => word.length > 6);

console.log(newWords); 
```

```javascript
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

function myfilter(origin, callback) {
    
    const result = [];

    for (let i = 0; i < origin.length; i++) {
        const current = origin[i];
        if (callback(current)) {
            result.push(current);
        } 
    }
    return result;
}

const newWords = myfilter(words, element => element.length > 6);
console.log(newWords);
```
