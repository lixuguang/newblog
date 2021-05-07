---
title: NodeJS 下 RESTful 架构的最佳实践（课堂笔记）
date: 2020-02-11 16:00:00
categories: 
	- NodeJS
tags:
	- NodeJS
	- RESTful
---
> 作者：李旭光
> 引用请标明出处

## 什么是 RESTful
> REST 指的是一组架构约束条件和原则。满足这些约束条件和原则的应用程序或设计就是 RESTful。

实际上就是 url 风格/ 路由风格 ， 给出一种写路由的原则  ---  面向资源
tip：静态路由利于seo优化 api/news/1，动态路由不利于seo优化 api/news?id=1

原来的风格
| 路由 | 功能 | 描述 |
| ---- | ---- |—---|
| http://127.0.0.1/user/query/1     | 查询 | 根据ID查询用户信息 |
| http://127.0.0.1/user/save        | 保存 | 注册用户 |
| http://127.0.0.1/user/update      | 更新 | 修改用户 |
| http://127.0.0.1/user/delete/{id} | 删除 | 删除用户 |

RESTful --- 面向资源：对于同一个资源都在同一个 URL 进行，通过判断 HTTP 请求的类型来决定做不同的事
/user 一个资源
| 路由                    | 请求类型 |
| ----------------------- | -------- |
| http://127.0.0.1/user/1 | GET      |
| http://127.0.0.1/user   | POST     |
| http://127.0.0.1/user   | PUT      |
| http://127.0.0.1/user   | DELETE   |

URL 描述资源 ， HTTP 描述请求。（HTTP协议无语义无状态 ---- 降低复杂度，业务无关 --- 架构代码业务无关)

RESTful 采用的是顶层路由
> 顶层路由设计：不需要有物理文件映射路由

``` js
// express
// app.js
const express = require('express')
const app = express()
app.get('/case.avi',(req, res)=>{
	res.send('hello world'); // 不需要对应物理文件
})
app.listen(3000)
```
原生接口
``` js
// index.js
const http = require('http');
const mysql = require('mysql'); // mysql
const co = require('co-mysql') // 异步同步化
const md5 = require('md5-node') // md5加密

// 连接数据库
let db = mysql.createPool({ // 连接池自己管理 不用关闭
	host:'localhost', 
	user:'root', 
	password:'root',
	database:'user'
})
let conn = co(db)

const app = http.createServer(async (req,res)=>{
	if(req.method === 'POST'){
		if(req.url === '/user'){
			// res.end(JSON.stringify({'message':'对user发起post请求'}))
			req.on('data', async (data)=>{
				arr.push(data)
			})
			req.on('end',async ()=>{
				let buffer = Buffer.concat(arr);
				// json对象
				let {username,pasword} = JSON.parse(buffer.toString())
				// console.log(username,pasword)
				let sql = `selct user from admin where user = ${username}`
				let data = await conn.query(sql);
				// console.log(data)
				if(data.length >=1 ){
					res.end(JSON.stringify({
						'status':200,
						'message':'用户名已经注册'
					}))
				}else{
					// 写入数据库
					password = md5(password);
					let sql = `INSERT INTO admin (user,password) VALUES ('${username}','${password}')`
					await conn.query(sql);
					res.end(JSON.stringify({
						'status':200,
						'message':'注册成功'
					}))
				}
			})
		}
	}if(req.method === 'GET'){
		if(req.url === '/user'){
			// res.end(JSON.stringify({'message':'对user发起get请求'}))
			let sql = `SELECT id,user,password FROM admin`
			let data = await conn.query(sql);
			res.end(JSON.stringify(data))
		}
	}
}).listen(3000)

// .http 文件
@url = http://localhost:3000
@type = Content-Type: applications

GET {{url}}/user HTTP/1.1

POST {{url}}/user HTTP/1.1
{{type}}

{
	username:'admin',
	password:123456
}
```
使用express实现(express --- generater yard ,koa --- async await)
``` js

const express = require('express')
const app = express()
const mysql = require('mysql'); // mysql
const co = require('co-mysql') // 异步同步化
const md5 = require('md5-node') // md5加密
const bodyparse = require('body-parse')
// 连接数据库
let db = mysql.createPool({
	host:'localhost', 
	user:'root', 
	password:'root',
	database:'user'
})
let conn = co(db)

app.use(bodyparse.urlencoded({
	extended:true // 返回对象是兼职对，false - string/array true - any
}))
app.use(bodyparse.json())

app.post('/user',async (req.res)=>{
	let { username , password} = req.body
	// console.log(username,pasword)
	let sql = `selct user from admin where user = ${username}`
	let data = await conn.query(sql);
	// console.log(data)
	if(data.length >=1 ){
		res.send(JSON.stringify({
			'status':200,
			'message':'用户名已经注册'
		}))
	}else{
		// 写入数据库
		password = md5(password);
		let sql = `INSERT INTO admin (user,password) VALUES ('${username}','${password}')`
		await conn.query(sql);
		res.send(JSON.stringify({
			'status':200,
			'message':'注册成功'
		}))
	}
})

app.get('/user/:id',(req,res)=>{
	res.send(req.params.id)

	let sql = `SELECT id,user,password FROM admin WHERE id = ${req.params.id}`
	let data = await conn.query(sql);
	res.end(JSON.stringify(data))
})

app.listen(3000)
```

使用koa实现
``` js


// config.js
module.exports = {
	host:'localhost', 
	user:'root', 
	password:'root',
	database:'user'
}

// libs/database.js
const config = require('../config')
const mysql = require('mysql'); // mysql
const co = require('co-mysql') // 异步同步化
// 连接数据库
let db = mysql.createPool({
	host:config.host, 
	user:config.user, 
	password:config.password,
	database:config.database
})
let conn = co(db)

// router/user/index.js
const Router = require('koa-router')
const md5 = require('md5-node') // md5加密
const router = new Router();

router.get('/user',async ctx=>{
	ctx.body = '主页'
})
router.post('/user',async ctx=>{
	let {username,password} = ctx.request.body
	// console.log(username,password)
	ctx.body = {
		username,password
	}
})
module.exports = router.routes();

// app.js
const koa = require('koa')
const Router = require('koa-router')
const body = require('koa-bodyparse')
const config = require('config')
const app = new Koa()
const router = new Router()
app.context.db = require('./libs/database')
app.context.config = config
app.use(body())
router.use('/api',require('./router/user'))
app.use(router.routes())
app.listen(3000)
```