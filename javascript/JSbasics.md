
## javascript基础

**引入js文件**

`<script src="js/require.js" defer async="true" ></script>`

async属性表明这个文件需要异步加载，避免网页失去响应。IE不支持这个属性，只支持defer，所以把defer也写上。

#### 什么是js
Javascipt是一种脚本语言，由web浏览器进行解释和执行

JavaScript一种直译式脚本语言，是一种动态类型、弱类型、基于原型的语言，内置支持类型。

<em>
弱类型即数据类型可以被忽略的语言,一个变量可以赋予不同数据类型的值
</em>

ECMAScript : 核心

DOM : 文档对象模型

BOM : 浏览器对象模型

> 执行js代码会阻塞线程(js引入代码放在body结束`</body>`标签前)

## 数据类型

基本数据类型有undefined、null、Boolean、number、string

复杂数据类型为object(function,array)

基本类型变量存的是值，复杂类型的变量存的是内存地址

> 注意：var a=1,b=2;(a=1,b=2)，var a,b=1;(a=undefined,b=2)


* 1.字符串(String)

字符串（非纯数字类型）转化为数字会报错
```
var str="string";
console.log(parseInt(str)); //NAN (not a number)
```

如果是

`var str="10string";`

会输出为10；

* 2.数值(number)

* 3.布尔类型 (boolean)

* 4.对象(object)

访问对象里面的（静态）属性通过"."来访问

若使用"变量"里动态访问属性，则使用"[]"来访问

```
var obj={
  name:"zhangsan";
  age:"18";
  sex:"male"
}
console.log(obj.age);
var aa="age";
console.log(obj[aa]);

```

* 5.Array 数组(object)

* 6.null, undefined 

**Null**

在 JavaScript 中 null 表示 "什么都没有"。(没有值，没有对象)

null是一个只有一个值的特殊类型。<span>值为空，指针为空</span>，表示一个空对象引用。

`var person = null;   `     你可以设置为 null 来清空对象

**Undefined**

在 JavaScript 中, undefined 是一个没有设置值的变量。（默认值，值未定义）

typeof 一个没有值的变量会返回 undefined。

`var person = undefined; `   你可以设置为 undefined 来清空对象

**Undefined 和 Null 的区别**
```
typeof undefined             // undefined
typeof null                  // object(历史遗留的坑)
null === undefined           // false
null == undefined            // true
```	

**总的来说**

undefined:已经声明变量,但没有赋值

Null:连变量都没有声明

* 7.symbol

Symbol()函数会返回symbol类型的值，该类型具有静态属性和静态方法。

它的静态属性会暴露几个内建的成员对象；它的静态方法会暴露全局的symbol注册，且类似于内建对象类，但作为构造函数来说它并不完整，因为它不支持语法："new Symbol()"。

每个从Symbol()返回的symbol值都是唯一的。一个symbol值能作为对象属性的标识符；这是该数据类型仅有的目的。




## 函数
* **1、函数声明式**

`function sum(){}`

```
aa();
function aa(){
	console.log("aa");  //aa
}
```


* **2、函数的表达式**
	
`var sum = function(){}`

```
bb();
var bb=function(){
	console.log("bb");  //bb is not a function
}
```

> 函数声明式的函数会提前声明，函数的表达式不会

* **3、调用函数**

```
function say(){
alert("哈哈");}

say();
```

```
function say(str){
alert(str);}

say("哈哈"); //相等于window.say("哈哈")；

say.call(window,"哈哈");

say.apply(window,["哈哈"]);
```

* **4.传参**

##### **arguments**
保存传进来的实参,arguments不是数组，不可以使用数组方法

`console.log(Object.prototype.toString.call(arguments)); //[object Arguments]`

```
aa([1,2,3],10);
function aa(){
	console.log(arguments);  //Arguments(2)
	 //如果要使用数组方法，console.log(Array.prototype.slice.call(arguments,1));
	
	console.log(a);  //10
}
```

> call和apply    
call方法传入多个参数使用逗号, 例如:x.call(this,1,2);    
apply方法传入多个参数使用[], 例如:x.apply(this,[1,2]);

######### return
```
function say(){
alert("哈哈");
return
document.write("啊");
}

return 后面的代码不会执行

```

######### typeof

`var str="我是字符串"；`

`var a=function(){};`

`var bb=true;`

`var obj=new object{};`

`var arr=new array()`

`console.log(typeof obj);`//object

`console.log(typeof arr);`//object

`console.log(typeof str); `//"string"
			
> typeof不能判断数组和对象

**判断数据arr和对象obj；**

`console.log(Object.prototype.toString.call(arr));` //[object Array]

`console.log(Object.prototype.toString.call(obj));` //[object Object]

## 申明变量

**var**

声明一个变量 使用var关键字

js作为一种弱类型语言,es5之前有一种特点:允许变量"重复声明"

var 坑点    
1.可以重复声明    
2.变量声明提前(在函数体里面体现)    
```
console.log(a);
var a=10;
			
输出的不是 a is not defined,而是undefined
```

> 为什么呢？


```
实际上在执行语句之前所欲变量在底层已经声明了

实际上下面的代码才是正真的执行
var a
console.log(a);
var a=10;
```

> es6填坑，推出了const let 关键字 声明变量

**const**

声明一个定值，不存在声明提前

```
console.log(c);
const c="abc";

//c is not defined
```

```
const c="abc";
const c=10;

//Identifier 'c' has already been declared
已经声明变量，值不可改变
```

```
const c=[1,2];
c.push(3);
console.log(c); //[1, 2, 3]

数组是引用类型，c指向的是数组的指针，不知数组的值，如果c={}时指针改变了就会报错
```

**let**

声明一个定值，不存在声明提前

```
let i="100";
i=666;
console.log(i);  //666

与const不同，let可以重复声明
```

**let是块级作用域，函数内部使用let定义后，对函数外部无影响。**

```
let c = 3;
console.log('函数外let定义c：' + c);	//输出c=3
function change(){
let c = 6;
console.log('函数内let定义c：' + c);	//输出c=6
} 
change();
console.log('函数调用后let定义c不受函数内部定义影响：' + c);	//输出c=3
```

例子：
```
for(let i=1;i<=3;i++){
				setTimeout(function(){
					console.log(i);  //1 2 3
				},0)
			}
			
for(var i=1;i<=3;i++){
				setTimeout(function(){
					console.log(i);  //4 4 4
				},0)
			}
```

#### ==和===

**1、对于string,number等基础类型，==和===是有区别的**

1）不同类型间比较，==之比较"转化成同一类型后的值"看"值"是否相等，===如果类型不同，其结果就是不等

2）同类型比较，直接进行"值"比较，两者结果一样

**2、对于Array,Object等高级类型，==和===是没有区别的**

进行"指针地址"比较

**3、基础类型与高级类型，==和===是有区别的**

1）对于==，将高级转化为基础类型，进行"值"比较

2）因为类型不同，===结果为false


**总的来说**

==比较的是两个变量的值，只要值相等，返回一个true（涉及"隐式转换"）

===除了比较两个变量的值以外，还会比较两者的数据类型；若两个的数据类型相同，则返回true，其它情况返回false。

**!=和!==同理**

#### 作用域
window  全局作用域    
函数{}  局部作用域

调用函数，产生一个执行环境--作用域链。

```
**es5没有块级作用域**
for循环后,依然可以访问i的值
for(var i=0;i<=10;i++){}
console.log("var i"+i);  //10
			
**es6支持块级作用域**
for(let j=0;j<=0;j++){};
console.log("let j"+j);  //j is not defined
```

> for(var i=0;i<=10;i++)相当于var i；for(i=0;i<=10;i++)，声明提前了，全局变量不会被回收

##### 全局变量

挂载在window下的变量，叫做全局变量，可以在任意地方访问。

##### 局部变量

在定义该变量的函数中，可以访问，全局无法访问局本变量。

## 闭包(closure)
有权访问外部变量的函数 （有权访问外部变量或者函数的执行环境 ）

**例子1：**
```
function a(){
var aa="a的局部变量";
				
  function c(){
		var cc="c的局部变量";
		console.log(aa);
	}
}
a();
			
函数c对aa变量始终保持引用
aa形成闭包，始终保存在内存里，不会被回收机制回收。
```

**例子2：**
```
<ul>
	<li>我是1</li>    //点击alert(3);
	<li>我是2</li>    //点击alert(3);
	<li>我是3</li>    //点击alert(3);
</ul>
```

```		
		<script type="text/javascript">
			var li=document.querySelectorAll("li");
			for(var i=0;i<li.length;i++){
				li[i].onclick=function(){
					alert(i);
				}
			};
		</script>
		
原因：for循环给每个li注册点击事件，for循环结束时i=3；
当点击触发function()函数时，输出的i会从外部找，此时i值为3，
所以不管点击哪一个li标签都会输出3.
```

**解决方法1：** 把var改为let
> var和let的区别在“声明变量”有详细解释

**解决方法2：**
```
var li=document.querySelectorAll("li");
			for(var i=0;i<li.length;i++){
			
				(function(k){
				
					li[k].addEventListener("click",function(){
						alert(k);
					})
					
				})(i)
				
			};
			
for循环结束后，在内存里面形成了3个闭包
```
> 滥用闭包，可能会出现内存泄露的问题（大量占用内存）

> 用let声明的变量，持久化保留在内存里面

## 变量的命名方式

1、变量名字，必须要以"字母"，"_"或"$"开头，不允许使用数字，其它符号。

2、变量名区分大小写的

3、不能使用关键字或保留字

4、变量名中不得使用空格


#### 命名规范

**1、小驼峰法**

var oldValue=1;

<em>
css里的border-color

js里的-为减号运算符

故写成borderColor
</em>
   

## setInterval 和 clearInterval
设置循环定时器和清除循环定时器

```
			setInterval(function(){
				document.write(new Date()+"<br/>")
			},1000)
```			



## setTimeOut 和 clearTimeOut
设置定时器和清除定时器

一次性计时器
```
			setTimeout(function(){
				document.write(new Date()+"<br/>")
			},1000)
```

## requestAnimationFrame
根据显示器的刷新频率来刷新（h5新增计时器）

```
			requestAnimationFrame(
				function(){
				document.write(new Date()+"<br/>")
			}
			)
```