---
title: "new: chart_graph"
date: 2022-11-18
last_modified_at: 2022-11-18
category: study
tags: [Node.js, Express, MySQL, Chart.js, fetch]
---

# new: chart_graph

```javascript
const express = require('express')
const mysql = require('mysql')
const path = require('path')
const static = require('serve-static')
const dbconfig = require('./config/dbconfig.json')

const pool = mysql.createPool({
	connectionLimit:10,
	host:dbconfig.host,
	user:dbconfig.user,
	password:dbconfig.password,
	databse:dbconfig.database,
	debug:false,
	timezone: '09:00'
})

const app = express()
app.use(express.urlencoded({extended:true}))
app.use(express.json())
app.use('/public', static(path.join(__dirname, 'public')))
// 위와 같은 코드를 통해 사용자가 Client에 접속할 때, public 경로에 자동적으로 들어가도록
// 만들어준다. /public에는 html 파일들이 존재한다.

app.post('/chartdatafromdb_elec', (req, res) => {
	const bid = req.body.bid
	
	pool.getConnection((err, conn) => {

		// 먼저, 기본값을 세팅해준다.
		// 기본은 result를 error 상태로 세팅해준다.
		const resData = {}
		resData.result = 'error'
		resData.temp = []
		resData.reg_date = []

	if (err) {
		pool.releaseConnection(conn)
			res.json(resData) // express를 사용할 때는 json을 res.json(json_file)을 이용하면
												// Client에게 json data를 전송한다.
		return
	}

	const exec = conn.query('select `value`, `regdate` from `INU_SmartCampus`.`BldngEle`
				where `bldng_name`=? and `regdate` > "2021-12-20 00:00:00" order by 
				`regdate` asc',
				[bid], (err, rows) => {
			
				// err가 발생할 경우, pool의 connection을 해제하고 현재까지 만들어진 json data를
				// Client에 전송한다.
				if (err) {
					pool.releaseConnection(conn)
					res.json(resData)
					return
				}
			
				// 여기까지 왔다는 것은, query가 정상적으로 실행됐다는 뜻이다.
			
				if (rows[0]) {
					resData.result = 'ok'
					rows.forEach((val) => {
						resData.temp.push(val.value)
						resData.reg_date.push(val.regdate)
					})
			// 위에서, 사전에 resData의 temp와 reg_date는 array로 선언되었으므로 push를 통해
			// query에서 가져온 data를 넣어준다.
				} else {
					resData.result = 'none'
				}
			
				return res.json(resData)
		})
	})
})

app.post('/chartdatafromdbwithbid', (req, res) => {

	const bid = req.body.bid
	
pool.getConnection((err, conn) => {
	const resData = {} // dictonary. key, value 형태로 되어 있음. 중괄호
	resData.result = 'error'
	resData.temp = [] // empty Array
	resData.reg_date = [] // empty Array

	if (err) {
		// conn.release()
		pool.releaseConnection(conn)
		res.json(resData) // json data를 Client에게 전송한다.
		return
	}

	// 여기까지 왔다면, pool에서 Connection을 err없이 얻어왔다는 뜻이므로, query를 보낸다.

	const exec = conn.query('select `temprerature`, `reg_date` from `building_temperatur`
			where `building_id`=? order by `reg_date` by asc', [bid],
			(err, rows) => {
				
				if (err) {
					// conn.release()
					pool.releaseConnection(conn)
					res.json(resData)
					return
				}
		
				// rows[n]은 query를 통해 얻어온 하나의 row를 의미한다.
				// 따라서, 각각의 rows의 element에는 temperature, reg_date column이 있을 것이다.
				// forEach를 통해 모든 row를 check하면서, resData에 값을 추가해준다.
				if (rows[0]) {
					resData.result = 'ok',
					rows.forEach((val) => {
						resData.temp.push(val.tempratrue)
						resData.reg_date.push(val.reg_date)
					})
				} else { // 데이터가 존재하지 않는 경우. rows가 비어있다.
					resData.result = 'none'
				}
		
				return res.json(resData) // Client에 json data를 보내준다.
		})
	})
})

app.listen(3000, () => {
	console.log('server started at 3000)
})
```

---

main.html : building id를 server에 보내서 이에 해당하는 데이터를 mySQL에서 query함으로써 건물의 온도 데이터를 가져온다. 이전에 사용했던 form은 입력한 데이터를 body로 구성하여 json으로 server에 전송해주었지만, 이번에는 fetch를 사용하여 HTTP method(post) header, body를 모두 구성하여 server에 전송한다. 또한, server에 HTTP request를 전송한 후, 바로 코드가 실행되지 않도록 promise를 걸어주어서 동기적인 동작을 할 수 있도록 만들어준다.

```html
<!DOCTYPE html>
<html>
    <head>
        <!-- chart를 그리기 위해 사용되는 library -->
        <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js"></script>
        <!-- https://cdn.jsdelivr.net/npm/chart.js 이 옵션으로도 가능하다. 위는 버전을 직접 지정해주는 것으로 보인다. -->
    </head>
    <body>
        <!-- Web Browser의 크기에 따라 달라지지만, 비율은 맞추면서 크기가 바뀐다. -->
        <!-- 실제 그림이 그려질 div, canvas. -->
        <div>
            <canvas id="myChart" width="400" height="320"></canvas>
        </div>
        <div>
            <label>빌딩 ID</label>
            <input type="text" id="buildingid">
            <button id="chartdatafromdb">그래프 그리기</button>
        </div>
    </body>
    <script>
        
			<!-- js 내용은 하단에! -->

    </script>
</html>
```

```javascript
let myChart = undefined

        // chartdatafromtdb button을 클릭하면 발생할 이벤트를 정의하는 addEventListener 
        const btn = document.getElementById('chartdatafromdb')
        const bid = document.getElementById('buildingid')
        btn.addEventListener('click', () => {
            // svr에 데이터를 요청하고, 데이터를 받아서 그래프를 그린다.
            console.log('buildingid : %s', bid.value)
            chartDataFromDB(bid.value)
        })

        // button이 눌렸을 때 발생할 event, 서버에 데이터를 요청하고 그림을 그린다.
        const chartDataFromDB = (bid) => {
            console.log('bid in charDataFromDB : %s', bid)
            // 서버에 데이터 요청
            fetch('/chartdatafromdbwithbid', {
                method : 'post',
                headers : {
                    'Content-type':'application/json'
                },
                // HTTP 통신할 때는 body에 인수를 붙여서 요청을 보낼 수 있음.
                body: JSON.stringify({
                    'bid':bid
                })
            }).then((res)=>res.json()) // res가 json으로 오므로, parsing을 해서 꺼내야 한다.
            .then((res)=>{
                // 받아온 데이터(res)를 가지고, 그래프를 그린다.
                // then callback result의 값을 보는 것이 중요하다. 상태를 알려준다.

                // res의 result가 진짜 있느냐, 이 문제는 서버에서 신경써야 할 문제이다.
                if (res.result != 'ok') {
                    console.log('result is not OK!');
                    return;
                }
                
                let reg_date_obj = [] // x축
                let temp_obj = [] // y축

                // res 안에는 데이터가 전달되는데,
                // result 안에는 'status'
                // temp : [ ... ]
                // reg_date : [ ... ]
                // 이와 같이 전달되므로, res.temp.length 만큼 반복시킨다.

                // 그래프를 그리기 위해,
                // reg_date_obj와 temp_obj를 만든다.
                for (let i = 0; i < res.temp.length; i++) {
                    reg_date_obj.push(res.reg_date[i])
                    temp_obj.push(res.temp[i])
                }

                // 그래프 그리기 위한 준비
                const data = {
                    labels : reg_date_obj,
                    datasets : [{
                        label : '온도',
                        borderColor : 'rgb(255, 99, 132)',
                        data : temp_obj  
                    }]
                }

                const config = {
                    type: 'line',
                    data: data,
                    options: {
                        responsive : true,
                        maintainAspectRatio : false
                    }
                }

                // 진짜로 그래프 그리기, 이 상태로만 남겨두면, 이미 그려진 그래프를 지우지 못해서
                // overwrite을 하지 못한다.

                // const myChart = new Chart(document.getElementById('myChart'), config)
                if (myChart !== undefined) { // 이전에 그려진 것이 있다면 지우고 다시 그린다.
                    myChart.destroy();
                }
                myChart = new Chart(document.getElementById('myChart'), config)
            })
        }
```

---

main3.html : 모두 같지만, building name을 기준으로 전기 사용량을 가져온다.

```html
<!DOCTYPE html>
<html>
    <head>
        <!-- chart를 그리기 위해 사용되는 library -->
        <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js"></script>
        <!-- https://cdn.jsdelivr.net/npm/chart.js 이 옵션으로도 가능하다. 위는 버전을 직접 지정해주는 것으로 보인다. -->
    </head>
    <body>
        <!-- Web Browser의 크기에 따라 달라지지만, 비율은 맞추면서 크기가 바뀐다. -->
        <!-- 실제 그림이 그려질 div, canvas. -->
        <div>
            <canvas id="myChart" width="400" height="320"></canvas>
        </div>
        <div>
            <label>빌딩 이름</label>
            <input type="text" id="buildingid">
            <button id="chartdatafromdb">그래프 그리기</button>
        </div>
    </body>
    <script>
        
			<!-- js 내용은 하단에! -->

    </script>
</html>
```

```javascript
let myChart = undefined

        // chartdatafromtdb button을 클릭하면 발생할 이벤트를 정의하는 addEventListener 
        const btn = document.getElementById('chartdatafromdb')
        const bid = document.getElementById('buildingid')
        btn.addEventListener('click', () => {
            // svr에 데이터를 요청하고, 데이터를 받아서 그래프를 그린다.
            console.log('buildingid : %s', bid.value)
            chartDataFromDB(bid.value)
        })

        // button이 눌렸을 때 발생할 event, 서버에 데이터를 요청하고 그림을 그린다.
        const chartDataFromDB = (bid) => {
            console.log('bid in charDataFromDB : %s', bid)
            // 서버에 데이터 요청
            fetch('/chartdatafromdb_elec', {
                method : 'post',
                headers : {
                    'Content-type':'application/json'
                },
                // HTTP 통신할 때는 body에 인수를 붙여서 요청을 보낼 수 있음.
                body: JSON.stringify({
                    'bid':bid
                })
            }).then((res)=>res.json()) // res가 json으로 오므로, parsing을 해서 꺼내야 한다.
            .then((res)=>{
                // 받아온 데이터(res)를 가지고, 그래프를 그린다.
                // then callback result의 값을 보는 것이 중요하다. 상태를 알려준다.

                // res의 result가 진짜 있느냐, 이 문제는 서버에서 신경써야 할 문제이다.
                if (res.result != 'ok') {
                    console.log('result is not OK!');
                    return;
                }
                
                let reg_date_obj = [] // x축
                let temp_obj = [] // y축

                // res 안에는 데이터가 전달되는데,
                // result 안에는 'status'
                // temp : [ ... ]
                // reg_date : [ ... ]
                // 이와 같이 전달되므로, res.temp.length 만큼 반복시킨다.

                // 그래프를 그리기 위해,
                // reg_date_obj와 temp_obj를 만든다.
                for (let i = 0; i < res.temp.length; i++) {
                    reg_date_obj.push(res.reg_date[i])
                    temp_obj.push(res.temp[i])
                }

                // 그래프 그리기 위한 준비
                const data = {
                    labels : reg_date_obj,
                    datasets : [{
                        label : '전기 사용량',
                        borderColor : 'rgb(255, 99, 132)',
                        data : temp_obj  
                    }]
                }

                const config = {
                    type: 'line',
                    data: data,
                    options: {
                        responsive : true,
                        maintainAspectRatio : false
                    }
                }

                // 진짜로 그래프 그리기, 이 상태로만 남겨두면, 이미 그려진 그래프를 지우지 못해서
                // overwrite을 하지 못한다.

                // const myChart = new Chart(document.getElementById('myChart'), config)
                if (myChart !== undefined) { // 이전에 그려진 것이 있다면 지우고 다시 그린다.
                    myChart.destroy();
                }
                myChart = new Chart(document.getElementById('myChart'), config)
            })
        }
```
