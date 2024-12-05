#### require.js安装
`npm install requirejs`

安装完成后，复制node_modules里requirejs文件夹里的require.js文件到自己的js文件夹，引入require.js。

`<script src="js/require.js" data-main="js/main.js" defer async></script>`

async属性表明这个文件需要异步加载，避免网页失去响应。IE不支持这个属性，只支持defer，所以把defer也写上。

data-main为入口点，

> 异步加载，不保证是在DOM渲染完后加载的

main.js
```
//定义一个模块
define("index",['./jquery-2.1.0'],function(){
	console.log("定义index模块，加载插件")
})

//加载模块
require(['index','10-16'],function(){
	console.log("加载模块")
})
```

其他js文件
```
define("10-16",[],function(){
	console.log("10-16")
})
```

**兼容非AMD规范的模块**
如果其他js的文件没有符合AMD规范，我们可以将其代码定义成一个模块

```
//定义一个模块
define("index",['./jquery-2.1.0'],function(){
	console.log("定义index模块，加载插件")
})

require.config({
//	指明模块路径
	paths:{
		'a':'./10-16'
	},
	shim:{
		'a':{
//			写上该模块的依赖,没有则为空
			deps:[],
//			将改造好的模块输出出来
			export:'a'
		}
	}
})

//加载模块
require(['index','./a'],function(){
	console.log("加载模块")
})
```