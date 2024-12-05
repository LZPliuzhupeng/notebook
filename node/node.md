
## 简单的MySQL语句

### **创建库**

create database 库名

`create database `userinfo` ;`

### **创建表**

create table 表名

`create table `usertable`(`id` int(11)  not null key auto_increment,`username`	char(16) not null,`password` 	char(16) not null);`

### **插值**

insert 表名 (字段1,字段2) values(值1,值2)

`insert `usertable` (username,password,id) values('admin','admin',123),('guest','guest',123);`

### **查询**

1、**where**

`SELECT * FROM `usertable` WHERE id=1;`

2、**order排序**

`SELECT * FROM `mark` WHERE `mark` >=90 ORDER BY `mark` DESC降序;`  

> DESC降序，ASC升序

## npm 安装mysql插件

`npm install mysql`

**连接数据库**

```js
		var mysql=require('mysql');
		var connection=mysql.createConnection({
			host:'localhost',
			user:'root',
			password:'12345',
			database:'test'
		});
//		创建连接
		connection.connect();
//		查询mark表里分数（mark）大于90的用户
		connection.query('select * from mark where mark>=90',function(error,results,fields){
			if(results){
				console.log(results)  //输出的格式为RowDataPacket
			}
		});
//		插入值
		connection.query('insert mark (username,user_id,mark) values ("飞机","555","100")',function(error,results,fields){
			if(results){
				console.log(results)
			}
		});
		connection.query('select * from mark where mark>=90',function(error,results,fields){
			if(error){throw error}
			let arr=[];
			results.forEach(x=>{
				arr.push(JSON.parse(JSON.stringify(x)))  //用JSON.stringify转为合法的json字符串
			})
			console.log(arr)
		});
		connection.end();
```

### 获取get、post请求的传过来的参数

**nodejs接受get参数**
```js
const http=require('http');
const url=require('url');
const querystring=require('querystring')

let serve=http.createServer((req,res)=>{
//		req.method请求方式
//		req.headers请求头

//		收到的url
		console.log(req.url.query); 
		
//		使用url模块的parse方法解析地址转为对象
		console.log(url.parse(req.url,true).query)  
//		解构赋值
		let {username,psw}=url.parse(req.url,true).query
		
		输出
		res.end(
			JSON.stringify({username,psw})
		)

}).listen("2222")
console.log('启动完成')
```

**nodejs接受post参数**
```js
const http=require('http');
const url=require('url');
const querystring=require('querystring')

let serve=http.createServer((req,res)=>{
//	nodejs字符串流,接受post参数,需要监听data事件,将chunk字符串拼接起来,将post数据收集到
	if(req.url=="favicon.ico"){return}
//	页面显示中文
	res.setHeader("Content-Type","text/html;charset=utf-8")
	
	let kongstr='';
	req.on("data",(chuck)=>{
		kongstr+=chuck;
		kongstr=querystring.parse(kongstr)
		
	})
	req.on("end",()=>{
		res.end(JSON.stringify(kongstr))	;
		res.end("成功")
	})

}).listen("2222")
console.log('启动完成')
```

### 根据api不同对数据库做不同操作

不同api的ajax请求
```js
let	url="http://localhost:2222/";
let urlstr1=url+'adduser?username='+username1+'&password='+password1+'&phonenum='+phonenum1
let urlstr2=url+'deleteuser?username='+username2
let urlstr3=url+'sreachuser?username='+username3
```

创建服务，连接数据库
```js
var mysql=require('mysql');
var url=require('url');
var http=require('http');
var connection=mysql.createConnection({
	host:'localhost',
	user:'root',
	password:'12345',
	database:'test'
});
```
```js
connection.connect();
http.createServer((req,res)=>{
	//	请求icon时，主动响应
	if(req.url=="/favicon.ico"){res.end('');return}
	//	处理get请求
	let {username,password,phonenum}=url.parse(req.url,true).query
	
	
	if(/adduser/.test(req.url)){
		let str=`insert user (username,password,phonenum) values('${username}','${password}','${phonenum}')`
		connection.query(str,function(error,results,fields){
			if(error){console.log(error);return}
			res.end(JSON.stringify(results))
		})		
	}
	if(/updateuser/.test(req.url)){
		let str=`update user set phonenum=222,password=222 where id=1`
		connection.query(str,function(error,results,fields){
			if(error){console.log(error);return}
			res.end(JSON.stringify(results))
		})
	}
	if(/deleteuser/.test(req.url)){
		let str=`delete from user where username='${username}'`
		connection.query(str,function(error,results,fields){
			if(error){console.log(error);return}
			res.end(JSON.stringify(results))
		})
	}
	if(/sreachuser/.test(req.url)){
		let str=`select * from user where username='${username}'`
		connection.query(str,function(error,results,fields){
			if(error){console.log(error);return}
			res.end(JSON.stringify(results))
		})
	}
//	如果产生跨域问题，添加白名单
	res.writeHead(200,{"Access-Control-Allow-Origin":"http://127.0.0.1:8020"})
	res.end('555')
	connection.end();
	
}).listen('2222');
console.log('启动完成')
```

### 用promise、async/await简单的封装

**promise.js**
```js
const mysql=require("mysql");

function getData(sql){
	return new Promise((resolve,reject)=>{
		//创建mysql实例
		let connection=mysql.createConnection({
			host:"localhost",
			user:"root",
			password:"12345",
			database:"test"
		});
		connection.connect();
		
		//构建原生sql语句,传给query方法处理数据	
		connection.query(sql,function(err,result){
			if(err){console.log(err);return}
			
			var obj=JSON.stringify(result);
				obj=JSON.parse(obj);
			resolve(obj);
		})		
		connection.end()
	})
}

module.exports={
	getData:getData
}
```

**http.js**
```js
const {getData}=require("./promise");
const http=require("http");

//用promisre写法把async去掉
http.createServer(async (req,res)=>{
	if(req.url=="/favicon.ico"){res.end("")}
	res.setHeader("Content-Type","text/html;charset=utf-8");
	let sql="select * from user where username='rrr'";
	
//	ES6 promise写法
//	getData(sql)
//	.then((result)=>{
//		res.end(JSON.stringify({msg:'promise.then出来的',data:result}))
//	})

	let result=await getData(sql);
	res.end(JSON.stringify({msg:'这是用await返回出来的',data:result}))
	
}).listen("3333")
```

> ES7  async/await。    
1.函数本身一定是 异步函数 async;普通函数 function a(){};异步函数 async function a(){}    
2.捕捉错误使用try{}catch(){}    
3.await 后面最好是一个promise对象


## pm2安装

`npm install pm2 -g`

**启动后台服务**

`pm2 start 项目路径`  （未开启热更新）

`pm2 start 项目路径 --wacth`  （开启热更新）

**更改开启进程的名字**(默认为文件名)

`pm2 start 项目路径 --name`

**查看后台状态**

`pm2 monit`

**查看正在开启的后台服务**

`pm2 list`

**删除正在执行的进程**

`pm2 delete id/name`(name默认为文件名)

**重启全部项目**

`pm2 reload all`

**重启单个项目**

`pm2 reload id/name`

**通过json文件启动**

```js
{
	"apps":[{
		"name":"sqltest",
		"script":"sql.json",
		"watch":true,
		"exec_mode":"fork"
	}]
}
```

`pm2 start json文件路径`

## express
基于 Node.js 平台，快速、开放、极简的 Web 开发框架

`npm install express --save`

```js
const express=require("express");
const app=express();
```
```js
//访问静态资源的中间件
//   http://localhost:4444/img/qq.jpg;设定前缀"/static",http://localhost:4444/static/img/qq.jpg
app.use("/static",express.static('public'))   
//多个静态资源文件夹，重复声明
app.use(express.static('files')) 

//设定路由
app.get("/",function(req,res){
	res.end("holle word")
})
app.get('/user/:id', function(req, res) {
  res.send('user ' + req.params.id+"  age"+req.query.age);
});

//出现404错误时所显示的警告
app.use(function (req, res, next) {
  res.status(404).send("Sorry can't find that!")
})

app.listen("4444",()=>{
	console.log("嘿嘿嘿")
})
```

post请求需引用`body-parser`中间件
```js
const bodyParser=require("body-parser")
app.use(bodyParser.urlencoded({extended:false}));
```

```js
app.post("/post",function(req,res){
	res.send(req.body.id)
})
```




## request
`npm install request --save`

```js
const express=require("express");
const app=express();
const request=require("request");
```

```js
app.get("/request",function(req,res){
	res.setHeader("Content-Type","text/html;charset=utf-8");
//	爬取taptap网站排行榜数据
	request("https://www.taptap.com/top/download",function(err,response,body){
		if(err){console(err);return}
	//	console.log(response)
	
//	提炼排行榜游戏名字
		let arr=body.match(/<h4>.*/g)
		res.end(JSON.stringify(arr))
	})
})

app.listen("5555",()=>{
	console.log("嘿嘿嘿")
})
```





