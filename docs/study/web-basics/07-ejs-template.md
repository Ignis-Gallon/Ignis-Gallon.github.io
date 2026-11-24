---
title: "ejs 템플릿"
date: 2023-02-05
last_modified_at: 2023-02-06
category: study
tags: [Node.js, ejs, 템플릿 엔진, Express]
---

# ejs 템플릿

ejs를 사용하면 node.js → .html 데이터 전달이 가능하다.

[[node.js][3] ejs 사용설명서](https://yahohococo.tistory.com/43)

ejs란, Embedded JavaScript의 약자로, 자바스크립트가 내장되어있는 html 파일이다. html에도 자바스크립트를 넣을 수 있지만 html 안에 내장되어 있는 것과 바깥에서 건드리는 차이이다.

ejs를 이용하면 html의 태그처럼 자바스크립트 내용을 삽입할 수 있다. 매우 큰 강점인데, 이것을 이용해 페이지를 동적으로 짜는 것이 수월해진다. 일반 html 파일은 무조건 &lt;script&gt; 태그를 이용해 분리를 시켜야하지만, ejs는 지정한 태그를 통해 스크립트 내용을 하나의 요소처럼 사용될 수 있게 한다. 또한, 서버에서 보낸 변수를 가져와 사용할 수 있다.

- 코드를 내장시키는 방법 &lt;% %&gt; 태그

```html
<html>
	<head>
		<title>test</title>
	</head>

	<body>
		<% for (var i = 0; i < 5; i++) { %>
			<h1>테스트 출력</h1>
		<% } %>
	</body>
</html>

<!-- 테스트 출력이 총 다섯 번 출력될 것이다. -->
```

&lt;% %&gt; 안에는 항상 JS코드가 들어가야 한다. 그리고 줄바꿈을 하면 새로운 &lt;% %&gt;를 이용해야 한다.

- 변수값을 내장시키는 방법 &lt;%= %&gt; or &lt;%- %&gt; 태그

표기법은 두가지이지만, 태그의 역할은 똑같다. 해당 태그 안의 변수 값을 그대로 코드로 옮겨주는 역할을 한다. 동적으로 페이지를 만들 때 아주 편하게 사용할 수 있는 기능이다. 이 태그를 썼을 때는 변수의 자료형은 고려하지 말고, 그냥 코드로 들어가있다고 생각해야 한다.

```html
<html>
	<head>
		<title>test</title>
	</head>

	<body>
		<% for (var i = 0; i < 5; i++) { %>
			<h1>테스트 출력 <%= i+1 %></h1>
		<% } %>
	</body>
</html>

<!-- 테스트 출력 1부터 5까지 출력될 것이다. -->
```

위와 같이 사용할 수도 있으며, &lt;% %&gt; 안에서 &lt;%= %&gt;, &lt;%- %&gt;를 혼용하여 사용할 수도 있다. 

&lt;%= %&gt; 태그 안에 있는 변수는 그 값이 코드처럼 들어가게 된다. 그래서 변수의 자료형이 문자열이라고 실수를 해서는 안된다. &lt;% %&gt;, &lt;%= %&gt; 태그는 ejs 안에 넣은 자바스크립트 코드에도 내장시킬 수 있다. 예를 들면,

```html
<html>
	<head>
		<title>test</title>
	</head>

	<body>
		<% for (var i = 0; i < 5; i++) { %>
			<h1>테스트 출력 <%= i+1 %></h1>
		<% } %>
	</body>
	<script>
		var test = <%= "test1" %>;
		alert(test);
	</script>
</html>
```

이렇게 사용하면 안된다. &lt;%= “test1” %&gt;은 예를 들기 위해 사용한 것이므로 이 방식으로 사용하지 않는 것이 좋다. &lt;%= %&gt; 태그는 그 안의 값을 무조건 ‘코드처럼’ 삽입한다. 문자열이든 숫자이든 그냥 그대로 코드로 옮겨진다. 개발자모드로 보면, var test에 “test1”이 저장되는 것이 아니라 변수명 test1을 저장하라는 코드로 나타난다.

그래서, 코드로 삽입하면서 문자열 형식으로 나타내고 싶을 때는 “&lt;%= %&gt;” 형식으로 코드를 작성하면 된다.

```html
<html>
	<head>
		<title>test</title>
	</head>

	<body>
		<% for (var i = 0; i < 5; i++) { %>
			<h1>테스트 출력 <%= i+1 %></h1>
		<% } %>
	</body>
	<script>
		var test = "<%= test1 %>";
		alert(test);
	</script>
</html>
```

- 서버에서 넘겨준 변수 사용하기

예를 들어, app.js에서 test2.ejs를 render하는 부분에 변수를 넘겨줘보자. 넘기는 변수는 object 형식으로 넘겨야하기 때문에 ‘변수명 : 값’으로 나타낸다. 구분은 콤마(,)로 한다.

```javascript
// ...
const ejs = require('ejs');
//...

app.set('view engine', 'ejs');

app.get('/hello', (req, res) => {

	res.render('test2', {
		one : 'this is one',
		two : 'this is two'	
	}
});
```

이렇게 하면 test2.ejs로 변수 one과 two가 넘어가게 된다. 그러면 test2.ejs로 넘어가서 one 과 two를 써보면 된다. 방법은 매우 간단하다. 그냥 넘겨진 변수명을 그대로 사용하면 된다.

```html
<html>
	<head>
		<title>test</title>
	</head>

	<body>
		<% for (var i = 0; i < 5; i++) { %>
			<h1>테스트 출력 <%= i+1 %></h1>
		<% } %>
		<h1><%= one %></h1>
		<h1><%= two %></h1>
	</body>
</html>
```

별 다른 방법 없이 서버에서 넘긴 변수명을 쓰면 ejs에서 그대로 가져와 사용할 수 있다.

## ejs 사용시 유의점

1. &lt;% %&gt;는 한 줄로 써야한다.
2. &lt;% %&gt;로 변수 선언을 하면 스코프 적용이 된다. ejs 내 자바스크립트와 &lt;% %&gt;안의 변수명은 겹쳐도 따로 인식된다.

    ⇒ 전역변수와 지역변수의 개념이 html 코드 안에서 똑같이 적용된다. 만약 &lt;script&gt; 태그 안의 &lt;% %&gt;를 같이 쓰다가 변수명이 겹치더라도 문제되지 않는다. html 내 코드와 &lt;% %&gt; 내 코드는 분리되어 있다.

3. var a = &lt;% = 1 %&gt;;은 가능하지만, &lt;% var a %&gt; = 1;은 불가능하다.

    ⇒ &lt;%= %&gt; 태그는 해당 변수의 값을 그대로 코드로 박아넣는 것이기 때문에 변수형을 가리지 않는다. 선언되지 않은 변수이면 undefined라는 코드를 그대로 갖다 넣는다. 그렇기 때문에 문자열로 저장하려면 “&lt;%= %&gt;” 와 같이 사용해야 한다.

4. &lt;% %&gt; 내 코드는 개발자 모드로 볼 수 없다.

    ⇒ 내장된 &lt;% %&gt; 자바스크립트 코드는 개발자 모드로 봐도 볼 수 없다.

5. 괄호를 정확히 여닫아야 한다.
