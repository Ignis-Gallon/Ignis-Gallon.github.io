---
title: "Scope Chaining"
date: 2023-07-23
last_modified_at: 2023-07-23
category: study
tags: [JavaScript, 스코프, 클로저]
---

# Scope Chaining

작성일 2023-07-23
{ .post-date }

- Scope Chaining은 JS의 핵심 개념중 하나로, 이를 이해하면 Closure, 실행 Context 등을 아주 쉽게 이해할 수 있다.

```javascript
window.addEventListener('load', () => {

	{
		let sun = 1
	}
	{
		let sun = 1
	}	
})
```

이와 같이 두 개의 공간을 별도로 만들면, 두 개의 태양이 뜰 수 있다. (Block)

```javascript
window.addEventListener('load', () => {

	{
		let sun = 1
		{
			let sun = 2
			console.log(sun)
		}	
	}
})
```

이와 같은 코드에서 console.log(sun)은 어떤 결과를 출력할까? console.log를 하게되면, 자신과 가까운 Block부터 해당하는 내용이 있는지 탐색한다. 즉, 자신과 같은 Block 내에 sun이 있으므로 이를 찾아내고 탐색을 멈춘다.

```javascript
window.addEventListener('load', () => {

	{
		let sun = 1
		{
			let moon = 2
			console.log(sun)
		}	
	}
})
```

만약 이와 같은 코드라면, console.log(sun)의 같은 Block 내에 moon이 없으므로, 다른 Block을 탐색하게 된다. 이때는 1이 출력된다.

```javascript
window.addEventListener('load', () => {

	{
		let sun = 1
		{
			let moon = 2
		}	
		console.log(moon)
	}
})
```

이와 같은 경우에는 moon의 Block이 이미 끝나서 할당이 해제되었으므로, moon을 출력할 수 없다는 오류가 발생한다.

---

이 개념들은 let, const, class keyword 등에 대해서는 모두 적용이 가능하나, var, function(함수 선언식) 등에는 적용되지 않는다. ⇒ 사용을 지양해야 함.

```javascript
window.addEventListener('load', () => {

	const save = () => {
		const value = 'ABCDE.....'
	}	

	save()
	console.log(value)
})
```

console.log(value)는 당연하게도 value 를 출력할 수 없다. save()는 위에 선언이 되어있으므로, 찾아가서 출력하게 된다.

```javascript
window.addEventListener('load', () => {

	const save = () => {
		const value = 'ABCDE.....'
		console.log(value)
	}	

	save()
})
```

코드를 위와 같이 수정하면 출력이 가능해진다.

```javascript
window.addEventListener('load', () => {

	const save = () => {
		const value = 'ABCDE.....'
		const ff = () => {
			console.log(value)
		}
	}	

	save()
})
```

⇒ ff가 save 내에서 선언됨

```javascript
window.addEventListener('load', () => {

	const save = () => {
		const value = 'ABCDE.....'
		const ff = () => {
			console.log(value)
		}
		ff() // 1
	}	

	save()
	ff() // 2
})
```

1은 save()를 실행할 경우 실행이 가능하나, 2는 실행되지 않음. (context 상에 없음)

```javascript
window.addEventListener('load', () => {

	const save = () => {
		const value = 'ABCDE.....'
		const ff = () => {
			console.log(value)
		}
		return ff
	}	

	const dd = save()
	dd()
})
```

이렇게 함수를 return해서 copy를 받고, 이를 실행하면 ff()를 외부에서 수행할 수 있게 된다.

```javascript
window.addEventListener('load', () => {

	const save = () => {
		const value = 'ABCDE.....'
	}	

	console.log(value)
})
```

save 함수 내에서 선언되었던 변수인 value는 외부에서는 출력할 수 없다. (메모리에서 자동으로 해제되기 때문이다.) 하지만, 이전에 봤던 dd 예제를 활용하면 간접적으로 출력이 가능하다.

```javascript
window.addEventListener('load', () => {

	const save = () => {
		const value = 'ABCDE.....'
		const ff = () => {
			console.log(value)
		}
		return ff
	}	

	const dd = save()
	console.log(dd())
})
```

ff를 return하는데, ff는 value라는 변수의 값을 얻어야만 하기 때문에 value를 보관하고 이후에 dd를 통해 사용될 수 있다. dd가 사용된 이후에 dd, save가 메모리에서 해제된다.
