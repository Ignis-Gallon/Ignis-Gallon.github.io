---
title: "생활코딩: async/await"
date: 2023-01-26
last_modified_at: 2023-03-12
category: study
tags: [JavaScript, 생활코딩, async/await, Promise]
---

# 생활코딩: async/await

작성일 2023-01-26 · 최종 수정일 2023-03-12
{ .post-date }

- timer (callback hell…)

```javascript
timer(1000, function() {
	console.log('작업');
	timer(1000, function() {
		console.log('작업');
		timer(1000, function() {
			console.log('작업');
			// ...
	});
});
```

⇒ 이런 문제를 해결하기 위해 등장한 것이 Promise이다.

```javascript
timer(1000)
	.then(function() {
		console.log('작업');
		return timer(1000);
	})
	.then(function() {
		console.log('작업');
		return timer(1000);
	})
	.then(function() {
		console.log('작업');
	})
```

⇒ 이 코드는 위와 똑같은 취지의 코드이지만, 각각의 작업들이 Chaining 되므로 callback hell로부터 벗어날 수 있게 된다.

---

그런데, 위의 동작을 동기적인 코드와 똑같이 동작하도록 만들어 문법적 단순함을 취할 수 있다.

```javascript
async function run() {
	await timer(1000);

	console.log('작업');
	await timer(1000);
	
	console.log('작업');
	await timer(1000);
	
	console.log('작업');
	// ...
}

// 실행

run();
```

이와 같이 Promise를 return하는 함수 앞에 await을 붙여줌으로써 구현이 가능하며, await이 붙어있는 Promise를 return하는 함수는, 반드시 async 함수 안에서 실행되어야만 한다.

```javascript
console.log('hello? 1');
getDynamicData()
    .then(data => {
        console.log(data);
    });

console.log('hello? 2');

getStaticData()
    .then(data => {
        console.log(data);
    });

console.log('hello? 3');
```

코드를 이런 식으로 동작시키면, “hello? 1” 부터 3까지가 먼저 출력되고, getStaticData, getDynamicData, 가 순서대로 출력된다.
