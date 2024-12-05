## html
* HyperText Markup Language 简称为HTML

* HyperText: 超文本 （文本 + 图片 + 视频 + 音频 + 链接）

* Markup Language: 标记语言

* H5 拥抱移动端
## 浏览器内核

Trident--Internet Explorer

Gecko-FireFox

Presto-Opera

Webkit-chrome

Chromium-chrome（2008）

## 申明文档类型
	<!DOCTYPE html>

## head
* 元数据
```
<meta charset="utf-8""> 申明页面的编码格式
<meta name="keywords" content="" />  关键词，可被搜索引擎搜到,多个关键词用逗号隔开(,)
<meta name="description" content=""/> 页面简介
```

## Input输入类型
* 描述` <input> `元素的输入类型。
输入限制


这里列出了一些常用的输入限制（其中一些是 HTML5 中新增的）：
```
属性   	描述
button	定义可点击的按钮（大多与 JavaScript 使用来启动脚本）
checkbox	定义复选框。
color	定义拾色器。
date	定义日期字段（带有 calendar 控件）
datetime	定义日期字段（带有 calendar 和 time 控件）
datetime-local	定义日期字段（带有 calendar 和 time 控件）
month	定义日期字段的月（带有 calendar 控件）
week	定义日期字段的周（带有 calendar 控件）
time	定义日期字段的时、分、秒（带有 time 控件）
email	定义用于 e-mail 地址的文本字段
file	定义输入字段和 "浏览..." 按钮，供文件上传
hidden	定义隐藏输入字段
image	定义图像作为提交按钮
number	定义带有 spinner 控件的数字字段
password	定义密码字段。字段中的字符会被遮蔽。
radio	定义单选按钮。
range	定义带有 slider 控件的数字字段。
reset	定义重置按钮。重置按钮会将所有表单字段重置为初始值。
search	定义用于搜索的文本字段。
submit	定义提交按钮。提交按钮向服务器发送数据。
tel	定义用于电话号码的文本字段。
text	默认。定义单行输入字段，用户可在其中输入文本。默认是 20 个字符。
url	定义用于 URL 的文本字段.
```

#### step
为每次增减的量

`<input type="number" name="points" min="0" max="100" step="10" value="30"> `

<input type="number" name="points" min="0" max="100" step="10" value="30">  


#### data
用于应该包含日期的输入字段。

`<input type="date" max="1979-12-31" min="2000-01-02">`
<input type="date" max="1979-12-31" min="2000-01-02">

 
#### Color
用于应该包含颜色的输入字段。
`<input type="color"> `
<input type="color"> 
 
#### Search
用于搜索字段（搜索字段的表现类似常规文本字段）。

`<input type="search"> `
<input type="search"> 

#### File
`<input type=“file” name=“photo”/>`

<input type=“file” name=“photo”/>

> 注意：
利用这项功能时，在 form 标签中要指定method属性。要把method 指定为post, enctype属性指定为 multipart/form-data。
    说明：multiple     控制是否上传多文件



## input属性
HTML5 为 <input> 增加了如下属性：

* autocomplete
* autofocus
* form
* formaction
* formenctype
* formmethod
* formnovalidate
* formtarget
* height 和 width
* list
* min 和 max
* multiple
* pattern (regexp)
* placeholder
* required
* step

并为 <form> 增加如需属性：

* autocomplete
* novalidate

#### autocomplete 属性（form）
* autocomplete 属性规定表单或输入字段是否应该自动完成。

当自动完成开启，浏览器会基于用户之前的输入值自动填写值。

提示：您可以把表单的 autocomplete 设置为 on，同时把特定的输入字段设置为 off，反之亦然。

autocomplete 属性适用于 <form> 以及如下 <input> 类型：text、search、url、tel、email、password、datepickers、range 以及 color。


#### novalidate 属性(form)
* novalidate 属性属于 `<form> `属性。

如果设置，则 novalidate 规定在提交表单时不对表单数据进行验证。

如：email的输入栏要xxx@xxx.coom规范输入


#### autofocus 属性
* autofocus 属性是布尔属性。

如果设置，则规定当页面加载时 `<input> `元素应该自动获得焦点。
	
####　formaction 属性
* formaction 属性规定当提交表单时处理该输入控件的文件的 URL。
* formaction 属性覆盖 <form> 元素的 action 属性。

formaction 属性适用于` type="submit"` 以及 `type="image"`。
```
<input type="submit" formaction="demo_admin.asp"
   value="Submit as admin">
```

#### formenctype 属性
* formenctype 属性规定当把表单数据（form-data）提交至服务器时如何对其进行编码（仅针对 method="post" 的表单）。
* formenctype 属性覆盖 <form> 元素的 enctype 属性。
* formenctype 属性适用于 type="submit" 以及 type="image"

#### formmethod 属性
* formmethod 属性定义用以向 action URL 发送表单数据（form-data）的 HTTP 方法。
* formmethod 属性覆盖 <form> 元素的 method 属性。
* formmethod 属性适用于 type="submit" 以及 type="image"。
```
<form action="/example/html5/demo_form.asp" method="get">
  First name: <input type="text" name="fname" /><br />
  Last name: <input type="text" name="lname" /><br />
<input type="submit" value="提交" />
<input type="submit" formmethod="post" formaction="/example/html5/demo_post.asp" value="使用 POST 提交" />
</form>
```

#### formnovalidate 属性
* novalidate 属性是布尔属性。

如果设置，则规定在提交表单时不对 `<input> `元素进行验证。
formnovalidate 属性覆盖` <form> `元素的 novalidate 属性。
formnovalidate 属性可用于` type="submit"`。
```
<input type="submit" formnovalidate="formnovalidate" value="进行没有验证的提交" />
```


#### formtarget 属性
* formtarget 属性规定的名称或关键词指示提交表单后在何处显示接收到的响应。
* formtarget 属性会覆盖 <form> 元素的 target 属性。

formtarget 属性可与` type="submit"` 和` type="image" `使用。

height 和 width 属性

height 和 width 属性规定 <input> 元素的高度和宽度。

height 和 width 属性仅用于 `<input type="image">`。

> 注释：
请始终规定图像的尺寸。如果浏览器不清楚图像尺寸，则页面会在图像加载时闪烁。

#### list 属性
* list 属性引用的` <datalist>` 元素中包含了`<input>`元素的预定义选项。
```
<input list="cars" />
<datalist id="cars">
	<option value="BMW">
	<option value="Ford">
	<option value="Volvo">
</datalist>
```

#### datalist和select区别

两个都是用来制作下拉菜单，而且两者还有一点小的不同。

对于select来说，select的下拉菜单是供用户选择的，用户只能选择其中的选项不能自己添加。

但是datalist就不同了，datalist不仅可以供用户选择，用户还可以自己输入，而且datalist还可以达到模糊匹配的效果，使用很方便。


#### min和max属性

min 和 max 属性规定` <input> `元素的最小值和最大值。
min 和 max 属性适用于如需输入类型：number、range、date、datetime、datetime-local、month、time 以及 week。
```
Enter a date before 1980-01-01:
<input type="date" name="bday" max="1979-12-31">

 Enter a date after 2000-01-01:
<input type="date" name="bday" min="2000-01-02">

 Quantity (between 1 and 5):
<input type="number" name="quantity" min="1" max="5">
```

#### multiple 属性（未知	）
* multiple 属性是布尔属性。

如果设置，则规定允许用户在` <input>` 元素中输入一个以上的值。

multiple 属性适用于以下输入类型：email 和 file。


#### pattern 属性
* pattern 属性规定用于检查 `<input> `元素值的正则表达式。

pattern 属性适用于以下输入类型：text、search、url、tel、email、and password。

> 
提示：请使用全局的 title 属性对模式进行描述以帮助用户。    
提示：请在我们的 JavaScript 教程中学习更多有关正则表达式的知识。

国家代码：
```
<input type="text" name="country_code" pattern="[A-z]{3}"
title="三个字母的国家代码" />
```

#### required 属性
* required 属性是布尔属性。

如果设置，则规定在提交表单之前必须填写输入字段。

required 属性适用于以下输入类型：text、search、url、tel、email、password、date pickers、number、checkbox、radio、and file.

#### step 属性
* step 属性规定 `<input> `元素的合法数字间隔。

示例：如果 step="3"，则合法数字应该是 -3、0、3、6、等等。

> 提示：step 属性可与 max 以及 min 属性一同使用，来创建合法值的范围。

step 属性适用于以下输入类型：number、range、date、datetime、datetime-local、month、time 以及 week。


## img
alt=""  当图片显示失败时显示文本

title=""  但鼠标悬停在图片上，显示文本

## a
target  指定连接打开方式  target=”_blank”(新页面打开)  target=”_self”（当前页面打开）    

锚点(运用于a标签)    
<a name=”h3n”>aa</a>
<a href=”#h3n”>返回</a>

## ul>li*10>a[href=”#”]{新闻$}

## 列表
```
<ol  start="10"  style="list-style-type:disc(圆点)/circle（圆圈）/square（正方形）""> 
   <li></li> 
</ol> 
   (ol 有序列表; ul 无序列表)
```

自定义列表
```
<dl>    
  <dt></dt>    
  <dd></dd>    
</dl>
```


## 表格
```
<table align=”center”></table>   //表格水平居中
<th colspan="2"></th>
<th rowspan="2"></th>
<table cellpadding="10"></table> （单元格间距，内容与边单元格宽的距离）
<table cellspacing="10"></table> （单元格边距，单元格与单元格之间的间距）
```

## 热区
		<img src="img/map.jpg" usemap="#hotmap" />
		<map name="hotmap">
			<area shape="rect" coords="298,20,400,85" href="#" />
			<area shape="rect" coords="170,117,277,203" href="#" />
		</map>


## 实体字符
大于号（>）  &gt;    
小于号（<）  &lt;    
引号（”）  &quot;    
注册商标(®)  &reg;    
版权(©)  &copy;    
&号  &amp;

##　textarea	
resize: none

浏览器内文本框默认为可拉伸，此属性设置为none

## base

控制整个页面所有外部链接的根地址

会自动拼接在该页面下面的所有相对地址的前面

例：
```
	 <head>
		<base href="/20160727HTML/" />
	</head>
	<body>
		<!– -
		这里的相对地址，会自动拼接base的值，即/20160727HTML/img/xx.jpg
		-- >
		<img src=“img/xx.jpg”/>
	</body>
```

## icon

`<link rel=“shortcut icon” href=“img/web.icon”/>`

<img src="../_img/icon_例子.png"></img>

## div

contenteditable

让你的页面变成一个编辑器:
```
			<div contenteditable=“true”> 
				This text can be edited by the user. 
			</div>
```



