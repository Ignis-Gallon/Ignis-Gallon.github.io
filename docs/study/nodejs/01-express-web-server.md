---
title: "Express 웹서버 만들기"
date: 2022-11-18
last_modified_at: 2022-11-18
category: study
tags: [Node.js, Express, npm, MySQL]
---

# Express 웹서버 만들기

- `npm init -y` : 현재 디렉터리를 새로운 프로젝트로 지정해준다.

    - npm : Node Package Manager 
    - init -y : init의 ask에 대해 모두 Yes라고 답한다.

이후, package.json file이 생성된다. (terminal에서 dir command로 확인 가능)  
package.json을 가지고 있으면, express가 필요하다는 것을 알 수 있다.  
이후 컴퓨터로 서버를 이전했을 때, 만약 express의 버전이 올라갔다면, 이전 버전에서 돌아가던 서버는 다른 dependency들의 버전을 알 수 없어서 새로운 버전으로 다운받게될 것이다.  
이를 방지하기 위해 express에 필요한 모든 dependencies의 버전 정보를 담아두는 것이  
package-lock.json file이다. `(버전의 호환성 문제없이 프로젝트를 진행할 수 있게 해준다.)`

- `npm install express --save` : express framework를 설치한다.

    - --save : package.json file에 해당 project에 대한 모든 정보를 저장하도록 만든다.  
        → 이와 같이 설정하면, 나중에 server를 옮길 때, package.json file과 js file만 가져가면
        그대로 다 옮길 수 있다.

이후, `node-modules`라는 directory로 들어가보면 `express module`이 설치된 것을 확인할 수 있으며, 그 외의 다양한 `dependencies (dependency packages)`가 설치된 것을 확인할 수 있다.

**`svr.js`**

```javascript
const { exec } = require('child_process')
const express = require('express')
const mysql = require('mysql') 
const path = require('path') 
const static = require('serve-static') 
const dbconfig = require('./config/dbconfig.json')

const pool = mysql.createPool({
    connectionLimit: 10, 
    host: dbconfig.host,
    user: dbconfig.user,
    password: dbconfig.password,
    database: dbconfig.database,
    debug: false
})

const app = express()

app.use(express.urlencoded({extended:true}))
app.use(express.json()) 

app.use('/public', static(path.join(__dirname, 'public')))

app.post('/process/login', (req,res)=> {

    console.log('/process/login 호출됨 '+req)
    const paramId = req.body.id; 
    const paramPassword = req.body.password;

    console.log('로그인 요청 '+paramId+' '+paramPassword);

    pool.getConnection((err, conn)=> {
        if (err) {
            console.log("Check")
            pool.releaseConnection(conn); 
            console.log("MySQL getConnection error. aborted")
            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
            res.write('<h2>DB서버 연결 실패</h2>')
            res.end()
            return; 
        }

        conn.query('select `id`, `name` from `users` where `id` =? and `password` = password(?)',
                    [paramId, paramPassword],        
                    (err, rows) => {
                        conn.release();
                        console.log('실행된 SQL Query: '+exec.sql)

                        if (err) {
                            console.dir(err);
                            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
                            res.write('<h2>SQL query 실행 실패</h2>')
                            res.end()
                            return;
                        }

                        if (rows.length > 0) {
                            console.log('아이디 [%s], 패스워드가 일치하는 사용자 [%s] 찾음', paramId, rows[0].name)
                            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
                            res.write('<h2>로그인 성공!</h2>')
                            res.end()
                            return;
                        } else {
                            console.log('아이디 [%s], 패스워드가 일치하는 계정 없음!', paramId)
                            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
                            res.write('<h2>로그인 실패! 아이디와 패스워드를 확인하세요.</h2>')
                            res.end()
                            return;
                        }
                    }
        )           
    })
});

app.post('/process/adduser', (req, res) => {

    console.log("/process/adduser 호출됨 "+req)

    const paramId = req.body.id; 
    const paramName = req.body.name; 
    const paramAge = req.body.age;
    const paramPassword = req.body.password;

  
    pool.getConnection((err, conn) => {
        

        if (err) {
            console.log("Check")
            pool.releaseConnection(conn);
            console.log("MySQL getConnection error. aborted")
            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
            res.write('<h2>DB서버 연결 실패</h2>')
            res.end()
            return; 
        }

        console.log("데이터베이스 연결 끈 얻었음...ㅎㅎ");
        const exec = pool.query('insert into users (id,name,age,password) values (?,?,?,password(?))',
                    [paramId,paramName,paramAge,paramPassword],
             
                    (err,result)=>{

                        pool.releaseConnection(conn);
                        console.log('실행된 sql: '+exec.sql)

                        if (err) {
                            console.log("SQL 실행시 오류 발생")
                            console.dir(err) 
                            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
                            res.write('<h2>SQL query 실행 실패</h2>')
                            res.end()
                            return
                        }
                        
                        if (result) {
                            console.dir(result)
                            console.log("Inserted 성공")

                            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
                            res.write('<h2>사용자 추가 성공</h2>')
                            res.end()
                        }
                        else {
                            console.dir(result)
                            console.log("Inserted 실패")

                            res.writeHead('200', {'Content-Type':'text/html; charset=utf8'})
                            res.write('<h2>사용자 추가 실패</h2>')
                            res.end()
                        }
                    }
        )
    })
})

app.listen(3000, () => {
    console.log("Listening on port 3000");
})
```

**`adduser.html`**

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
            <input type="submit" value="회원가입">
        </form>
    </body>
</html>
```

**`login.html`**

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

**`config.json`**

```json
{
    "host" : "127.0.0.1",
    "user" : "JDK",
    "password" : "보안을 위해 제거",
    "database" : "보안을 위해 제거"
}
```
