---
title: "new: login"
date: 2022-11-18
last_modified_at: 2022-11-18
category: study
tags: [Node.js, Express, MySQL, 로그인]
---

# new: login

```javascript
// server 코드에 필요한 초기 변수들을 설정한다.
const { exec } = require('child_process') // pool.releaseConnection(conn) 수행에 필요함.
const express = require('express') // framework express를 사용한다.
const mysql = require('mysql') // mysql을 사용한다. npm install mysql
const path = require('path') // package, 경로명이 길 때 간편하게 표현해줌.
const static = require('serve-static') // 이 프로그램을 돌릴 때만 svr.js가 있는 디렉토리를
// 최상위 디렉토리로 일시적으로 설정해준다. path와 관련이 있으며, 가장 윗조상이 누구인지
// 설정해주는 역할을 한다. 실제로 가장 윗조상은 login directory이지만, 이 프로그램을
// 돌릴 때에는 가장 윗조상이 svr.js라고 일시적으로 지정해줘서 경로를 찾을 수 있도록 한다.
// 즉, login directory가 있는 directory가 최상위인 것이 기본이고,
// const static = require('serve-static')을 이용하면 svr.js가 있는 directory가
// 일시적으로 최상위 디렉토리로써 동작한다는 의미로 보인다.

// mysql에서 connection을 얻어올 pool의 확인을 위한 json file 경로
const dbconfig = require('./config/dbconfig.json')

// pool을 얻어오기 위해, mysql.createPool을 통해서 pool을 생성한다.
// pool이란, mySQL과 통신하기 위해 connection이 필요한데, 이 connection을
// 모아두고 있다가 나눠주고 회수하는 곳을 pool이라고 한다. 따라서, const pool
// 을 통해서 10개의 connection을 보관할 pool을 만드는 것이라고 보면 된다.
const pool = mysql.createPool({
	connectionLimit: 10,
	host: dbconfig.host,
	user: dbconfig.user,
	password: dbconfig.password,
	database: dbconfig.database,
	debug: false
})

const app = express()
// webserver가 생김.

app.use(express.urlencoded({extended:true}) 
// url을 전송에 조금 더 유리한 형태로
// 바꾸어준다.
app.use(express.json()) // json으로 데이터가 와도 처리할 수 있도록 한다.

// 이것은, 현재 서버에 /process/login이라는 path로 Client가 요청하는 것에 대한 반환 코드를
// 작성하는 부분이다.
app.post('/process/login', (req,res) => {
	const paramId = req.body.id
	const paramPassword = req.body.password
	// 여기서 나오는 ID와 PW는 req를 통해서 전달된 것이다. 즉, Client측에서 데이터를 입력받아
	// server로 전송되었다는 뜻이다. json 형식으로 전달되었기 때문에, 분해해준다.

	// Client에서 사용자가 입력한 ID와 PW를 받았기 때문에, 이제 mySQL과 통신해서
	// 내가 받아온 것이 과연 맞는 정보인지 확인할 차례이다. 이전에 말했듯이, server가
	// mySQL과 통신하려면 가장 먼저 pool에서 connection을 받아야 하므로, getConnection을
	// 실시해준다.

	// 이미 위에서 선언한 const pool 변수를 통해서 connection을 받아온다. connection을
	// 받아올 때도, err, conn 두 개의 return이 넘어오게 되므로, 람다 함수를 작성해서
	// 각각에 대해서 모두 처리해준다.
	pool.getConnection((err, conn) => {
		
		// 먼저, err가 발생한 경우 해당 if문이 true가 된다.
		// pool에서 connection을 받아오는 과정에서 error가 발생한 것이므로, 먼저 pool을
		// releaseConnection(conn)해준다. 그 다음, status code 200으로 OK 상황으로 만들어
		// 주고, DB 서버 연결이 해제 됐다는 내용의 result를 반환해준다.
		if (err) {
			pool.releaseConnecton(conn)
			res.writeHead('200', {'Content-Type':'text/html; charset=utf8')
			res.write('<h2> DB 서버 연결 해제 </h2>')
			res.end()
			return
		}

		// 만약 여기까지 app.post가 실행되었다면, 정상적으로 pool에서 connection을 얻어온 것.
		// mySQL과 server가 통신할 수 있는 connection을 얻었으므로, 이제 query를 날리자.
		// pool.getConnection 에서 return받은 conn을 통해 mySQL과 connection을 이용 가능하다.
		conn.query('select `id`, `name` from `users` where `id` =? and `password` = password(?)',
								[paramId, paramPassword], // 이와 같은 방식으로 ?에 값 대입 가능
								(err, rows) => {
									// 성공하던 실패하던 어쨌든 pool의 connection을 release해준다.

									pool.releaseConnection(conn)
									
									if (err) {
										console.dir(err)
										res.writeHead('200', {'Content-Type':'text/html; charset=utf8')
										res.write('<h2> SQL query 실행 실패 </h2>')
										res.end()
										return
									}
									
									// rows.length > 0이라는 뜻은, login에 성공했다는 뜻.
									if (rows.length > 0) {
										res.writeHead('200', {'Content-Type':'text/html; charset=utf8')
										res.write('<h2> 로그인 성공! </h2>')
										res.end()
									}
									else {
										res.writeHead('200', {'Content-Type':'text/html; charset=utf8')
										res.write('<h2> 로그인 실패! </h2>')
										res.end()
									}
								}
		
				)
		})
})
```

이어지는 코드, user를 DB에 추가하는 코드이다.

```javascript
app.post('/process/adduser', (req, res) => {
	const paramId = req.body.id
	const paramName = req.body.name
	const paramAge = req.body.age
	const paramPassword = req.body.password

	pool.getConnection((err, conn) => {
		if (err) {
			pool.releaseConnection(conn)
			res.writeHead('200', {'Content-Type':'text/html; charset=utf8'}
			res.write('<h2> DB 서버 연결 실패 </h2>')
			res.end()
			return
		}

		// 여기까지 왔다는 것은, 성공적으로 mySQL의 pool에서부터 connection을 얻어왔다는 것이다.
		const exec = pool.query('insert into users (id, name, age, password) values (?, ?, ?, password(?))',
					[paramId, paramName, paramAge, paramPassword],
		
					(err, result) => {
						pool.releaseConnection(conn)
						
						if (err) {
							console.dir(err)
							res.writeHead('200', {'Content-Type':'text/html; charset=utf8')
							res.write('<h2> SQL query 실행 실패 </h2>')
							res.end()
							return
						}
					
						// 여기까지 왔다는 것은, query에서 err가 발생하지 않았다는 것이다.
						// 그러므로, result에 유의미한 값이 존재하게 될 것이다.
					
						if (result) {
							console.dir(result)
							res.writeHead('200', {'Content-Type':'text/html; charset=utf8'}
							res.write('<h2> 사용자 추가 성공 </h2>')
							res.end()
						}
						else {
							console.dir(result)
							res.writeHead('200', {'Content-Type':'text/html; charset=utf8'}
							res.write('<h2> 사용자 추가 실패 </h2>')
							res.end()
						}
					})
	})
})

app.listen(3000, () => {
	console.log('server start at port 3000')
})
```

---

/process/login

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>Sign-in 페이지 (feat. MySQL, NodeJS)</title>
    </head>
    <body>
        <h1>사용자 로그인</h1>
        <br>
        <form method="post" action="/process/login">
            <table>
                <tr>
                    <td><label>아이디</label></td>
                    <td><input type="text" name="id"></td>
                </tr>
                <tr>
                    <td><label>패스워드</label></td>
                    <td><input type="password" name="password"></td>
                </tr>
            </table>
            <input type="submit" value="로그인">
        </form>
    </body>
</html>
```

/process/adduser

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset = "UTF-8">
        <title>Sign-up page (feat. MySQL, NodeJS)</title>
    </head>

    <body>
        <h1>사용자 등록 페이지</h1>
        <br>
        <!-- 입력한 것을 서버에 전송하려면 formn이라는 형태로 만들어야 한다. -->
        <!-- POST방식으로 보냄. GET은 내용을 보낼 때, 데이터들이 다 보여진다.
             POST방식으로 하면 겉으로 드러나지는 않아서 조금 더 보안성이 높다. -->
        <form method="post" action="/process/adduser">
            <table>
                <tr>
                    <td><label>아이디</label></td>
                    <td><input type="text" name="id"></td>
                </tr>
                <tr>
                    <td><label>사용자명</label></td>
                    <td><input type="text" name="name"></td>
                </tr>
                <tr>
                    <td><label>나이</label></td>
                    <td><input type="text" name="age"></td>
                </tr>
                <tr>
                    <td><label>패스워드</label></td>
                    <td><input type="password" name="password"></td>
                </tr>
            </table>
            <input type="submit" value="회원가입"> <!-- submit을 누르면, 위에 input에서 입력된 값들이 입력된다... (입력 안하면 경고창 넣어줌) -->
        </form>
    </body>
</html>
```

form 

method=”post” : HTTP 통신 방식

action=”/…/…” : server에 post를 요청할 주소
