---
title: "반응형 웹"
date: 2023-07-23
last_modified_at: 2023-07-23
category: study
tags: [CSS, 반응형 웹, media query, container query]
---

# 반응형 웹

작성일 2023-07-23
{ .post-date }

```css
@media (max-width : 768px) {
	.text {
		font-size : 16px;
	}
}
```

⇒ 반응형 웹의 기본 설정. 브라우저의 width가 768px 이하면, 아래 속성을 적용한다는 뜻.

지금까지는 브라우저의 폭에 반응하는 CSS였지만, 컴포넌트 기반 웹개발의 유행에 따라서 CSS도 이러한 방식을 채택하게 되었다.

이제 부모 div Box에 영향을 받도록 코드를 짤 수 있다. (**container query**)

```css
.card-container {
	contain: style layout inline-size;
	width: 50%;
	border: 1px solid red;
	padding: 20px;
	transition: all 1s;
}
.title {
	font-size: 30px;
}

@container (max-width: 350px) {
	.title {
		font-size: 15px;
		color: grey;
	}
}
```

@container를 이용해, 부모 box의 크기에 반응하는 반응형 css를 적용 가능하다.
