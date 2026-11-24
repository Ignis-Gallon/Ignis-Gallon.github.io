---
title: "new: web_server"
date: 2022-11-18
last_modified_at: 2022-11-18
category: study
tags: [Node.js, Express, HTTP 상태 코드]
---

# new: web_server

HTTP Response status code : webserver가 client에게 보내는 코드이다. Client가 request를 요청하면, Webserver가 response를 보내준다. 이 때 Webserver가 통신의 상태를 표현하기 위해 보내주는 코드가 바로 status code이다. (Server가 Client에게 보내주는 코드)

sendStatus() : HTTP 통신에서 server가 client에게 status를 보내줄 때 사용하는 함수

```javascript
const express = require('express') // express를 사용한다.
const app = express() // express의 instance 'app'을 만든다.

// 람다 함수 형태로 client의 request, server의 response를 처리한다.
app.get('/', (req, res) => {
	res.sendStatus(200) // 200은 OK.
	// res.sendStatus(400) // 400은 Bad request.

	// 403 : forbidden => 금지된 자료
	// 404 : Not found => 올바른 요청을 보냈지만, 자료가 없음
	// 500 : Internal server error => 서버에서 문제가 발생
	// 503 : Service unavailable => 서버가 다운됨.
})

// 5500번 port에서 server의 동작을 시작한다.
app.listen(5500, () => {
	console.log('Start listening on 5500')
})
```

---

npm install express --save : 이 프로젝트를 돌리는데 express file이 필요하다는 것을 json에 기록함.
