---
title: "window.onload"
date: 2023-02-03
last_modified_at: 2023-02-04
category: study
tags: [JavaScript, window.onload, DOM]
---

# window.onload

작성일 2023-02-03 · 최종 수정일 2023-02-04
{ .post-date }

[javaScript - window.onload()란?](https://velog.io/@leyuri/javaScript-window.onload%EB%9E%80)

자바스크립트가 문서가 준비된 상황 이후에만 발동하도록 할려면 선언 위치는 상관 없어지는데, 이 때 사용하는 것이 바로 window.onload()이다.

```html
<script>
  window.onload = function(){
  var a = document.getElementById( 'test' );
  a.style.color = "blue"
  }
</script>
<p id = "test">hello</p>
```

이와 같이 window.onload라는 메서드를 오버라이딩 해주면 된다. 해당 함수 내의 코드 스크립트는 웹브라우저 내의 모든 요소가 준비되어야 실행될 수 있다. (웹브라우저를 담당하는 window라는 객체가, 웹 문서를 불러올 때 문서가 사용되는 시점에 실행되는 onload라는 함수를 다시 정의하는 것)

이를 통해서 해당 페이지에서 완전하게 웹문서 요소가 불러와졌을 때 실행할 코드를 설정할 수 있다.
