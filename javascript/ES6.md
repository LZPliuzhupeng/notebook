# ECMAScript 6
[阮一峰ECMAScript 6 入门](http://es6.ruanyifeng.com/)
## let和const
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

> 数组是引用类型，c指向的是数组的指针，不知数组的值，如果c={}时指针改变了就会报错
```

## 变量的解构赋值

### 数组的解构赋值

以前，为变量赋值，只能直接指定值。
```
let a = 1;
let b = 2;
let c = 3;
```
ES6 允许写成下面这样。
```
let [a, b, c] = [1, 2, 3];
```

**不借助第三变量实现变量交换**
```
let a=1;
let b=10;
[a,b]=[b,a]
```

**嵌套数组进行解构**
```
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

> 如果解构不成功，变量的值就等于undefined。    
例如：let [foo] = [];    
let [bar, foo] = [1];

**不完全解构**
```
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```

**如果等号的右边不是数组（或者严格地说，不是可遍历的结构），那么将会报错。**
```
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```

**默认值**
解构赋值允许指定默认值。
```
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
```
> 注意，ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined，默认值才会生效。

### 对象的解构赋值
```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```

> 数组的解构赋值是按次序取值的，对象是按属性名来取值的。

**变量名与属性名不一致**

`let { baz } = { foo: "aaa", bar: "bbb" };baz // undefined`

必须写成下面这样
```
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```

也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。
```
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```
> 上面代码中，foo是匹配的模式，baz才是变量。真正被赋值的是变量baz，而不是模式foo。

**嵌套结构**
```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```

> 注意，这时p是模式，不是变量，因此不会被赋值。如果p也要作为变量赋值，可以写成下面这样。

```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p, p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // ["Hello", {y: "World"}]
```

**默认值**
```
var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null
```
> 默认值生效的条件是，对象的属性值严格等于undefined。默认值生效的条件是，对象的属性值严格等于undefined。

**已经声明的变量用于解构赋值**
```
// 报错
let {foo: {bar}} = {baz: 'baz'};	
```

> 上面代码的写法会报错，因为 JavaScript 引擎会将{x}理解成一个代码块，从而发生语法错误。    
只有不将大括号写在行首，避免 JavaScript 将其解释为代码块，才能解决这个问题。

```
// 正确的写法
let x;
({x} = {x: 1});
```

### 字符串的解构赋值 
```
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```

**类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。**

```
let {length : len} = 'hello';
len // 5
```

### 数值和布尔值的解构赋值
**解构赋值时，如果等号右边是数值和布尔值(不是数组或对象)，则会先转为对象。**
```
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```
上面代码中，数值和布尔值的包装对象都有toString属性，因此变量s都能取到值。

> 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。

```
let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
```


### 函数参数的解构赋值
```
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```

> 在es5的时候这样类似的写法是传入一个数组，es6传入的是两个值

```
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```

**默认值**
```
function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]     {x = 0, y = 0} = {x: 3, y: 8}
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```

> 上面代码中，函数move的参数是一个对象，通过对这个对象进行解构，得到变量x和y的值。    
如果解构失败，x和y等于默认值。上面代码中，函数move的参数是一个对象，通过对这个对象进行解构，得到变量x和y的值。如果解构失败，x和y等于默认值。

**下面的写法会得到不一样的结果。**
```
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```

## 字符串的扩展

### includes(), startsWith(), endsWith()
传统上，JavaScript 只有indexOf方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6 又提供了三种新方法。

* includes()：返回布尔值，表示是否找到了参数字符串。
* startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
* endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

```
let s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```

这三个方法都支持第二个参数，表示开始搜索的位置。
```
let s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

> 上面代码表示，使用第二个参数n时，endsWith的行为与其他两个方法有所不同。    
它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。

### repeat()

repeat方法返回一个新字符串，表示将原字符串重复n次。
```
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

如果repeat的参数是字符串，则会先转换成数字。
```
'na'.repeat('na') // ""
'na'.repeat('3') // "nanana"
```

参数NaN等同于 0。
```
'na'.repeat(NaN) // ""
```

### padStart()，padEnd()
ES2017 引入了字符串补全长度的功能。

**如果某个字符串不够指定长度，会在头部或尾部补全。`padStart()`用于头部补全，`padEnd()`用于尾部补全。**
```
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

> 第一个参数用来指定字符串的最小长度，第二个参数是用来补全的字符串。

**如果省略第二个参数，默认使用空格补全长度。**
```
'x'.padStart(4) // '   x'
'x'.padEnd(4) // 'x   '
```

**如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。**
```
'xxx'.padStart(2, 'ab') // 'xxx'
'xxx'.padEnd(2, 'ab') // 'xxx'
```

**如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。**
```
'abc'.padStart(10, '0123456789')
// '0123456abc'
```

**常见用途**
```
'1'.padStart(10, '0') // "0000000001"
'12'.padStart(10, '0') // "0000000012"
'123456'.padStart(10, '0') // "0000123456"
```
```
'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

### 模板字符串
传统的 JavaScript 语言，输出模板通常是这样写的（下面使用了 jQuery 的方法）。
```
$('#result').append(
  'There are <b>' + basket.count + '</b> ' +
  'items in your basket, ' +
  '<em>' + basket.onSale +
  '</em> are on sale!'
);
```

ES6
```
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```

字符串中嵌入变量(原生js)
```
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

```
var div=document.createElement("div");
div.innerHTML=addhtml("Bob","today");

function addhtml(name,time){
	`Hello ${name}, how are you ${time}?`
}
```



## 数值的扩展

### Number.isFinite(), Number.isNaN()

**`Number.isFinite()`用来检查一个数值是否为有限的（finite），即不是`Infinity`。**
```
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
```

> 注意，如果参数类型不是数值，`Number.isFinite`一律返回`false`。

**`Number.isNaN()`用来检查一个值是否为`NaN`。**
(NaN--Not a Number)
```
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true' / 0) // true
Number.isNaN('true' / 'true') // true
```

> 如果参数类型不是`NaN`，`Number.isNaN`一律返回`false`。    
以往isNaN()属于windo对象的方法，只能在浏览器运行。    
ES6扩展到Number对象里，使之不限制于只能在浏览器运行。

### Number.isInteger()

`Number.isInteger()`用来判断一个数值是否为整数。
```
Number.isInteger(25) // true
Number.isInteger(25.1) // false
```

**JavaScript 内部，整数和浮点数采用的是同样的储存方法，所以 25 和 25.0 被视为同一个值。**
```
Number.isInteger(25) // true
Number.isInteger(25.0) // true
```

> 注意，由于 JavaScript 采用 IEEE 754 标准，数值存储为64位双精度格式，数值精度最多可以达到 53 个二进制位（1 个隐藏位与 52 个有效位）。如果数值的精度超过这个限度，第54位及后面的位就会被丢弃，这种情况下，`Number.isInteger`可能会误判。

```
Number.isInteger(3.0000000000000002) // true
```

上面代码中，`Number.isInteger`的参数明明不是整数，但是会返回true。原因就是这个小数的精度达到了小数点后16个十进制位，转成二进制位超过了53个二进制位，导致最后的那个2被丢弃了。	

### Math.trunc()
`Math.trunc`方法用于去除一个数的小数部分，返回整数部分。
```
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc(-0.1234) // -0
```

对于非数值，Math.trunc内部使用Number方法将其先转为数值。
```
Math.trunc('123.456') // 123
Math.trunc(true) //1
Math.trunc(false) // 0
Math.trunc(null) // 0
```

对于空值和无法截取整数的值，返回NaN。
```
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN
Math.trunc(undefined) // NaN
```

对于没有部署这个方法的环境，可以用下面的代码模拟。
```
Math.trunc = Math.trunc || function(x) {
  return x < 0 ? Math.ceil(x) : Math.floor(x);
};
```

### Math.sign()
`Math.sign`方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。

* 参数为正数，返回+1；
* 参数为负数，返回-1；
* 参数为 0，返回0；
* 参数为-0，返回-0;
* 其他值，返回NaN。

对于没有部署这个方法的环境，可以用下面的代码模拟
```
Math.sign = Math.sign || function(x) {
  x = +x; // convert to a number
  if (x === 0 || isNaN(x)) {
    return x;
  }
  return x > 0 ? 1 : -1;
};
```

## 数组的扩展

### Array.from()

Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

下面是一个类似数组的对象，`Array.from`将它转为真正的数组。
```
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

> es5转换为数组 `Array.prototype.slice.call()`

实际应用中，常见的类似数组的对象是 DOM 操作返回的 NodeList 集合，以及函数内部的`arguments`对象。`Array.from`都可以将它们转为真正的数组。
```
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).filter(p => {
  return p.textContent.length > 100;
});

// arguments对象
function foo() {
  var args = Array.from(arguments);
  // ...
}
```

上面代码中，`querySelectorAll`方法返回的是一个类似数组的对象，可以将这个对象转为真正的数组，再使用`filter`方法。


### 数组实例的 includes()

`Array.prototype.includes`方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的`includes`方法类似。ES2016 引入了该方法。
```
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```

该方法的第二个参数表示搜索的起始位置，默认为0。    
如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始。    
该方法的第二个参数表示搜索的起始位置，默认为0。如果第二个参数为负数，则表示倒数的位置，    
如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始。
```
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
```

`indexOf`方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于-1，表达起来不够直观。二是，它内部使用严格相等运算符（===）进行判断，这会导致对NaN的误判。	
```
[NaN].indexOf(NaN)
// -1
```
```
[NaN].includes(NaN)
// true
```

## 函数的扩展

### rest 参数

rest参数能够转化为真正的数组，可以直接使用数组的方法
```
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```

**下面是一个 rest 参数代替·arguments·变量的例子。**
```
// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}

// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();
```

> arguments对象不是数组，而是一个类似数组的对象。所以为了使用数组的方法，必须使用Array.prototype.slice.call先将其转为数组。    
rest 参数就不存在这个问题，它就是一个真正的数组，数组特有的方法都可以使用。


```
// 报错
function f(a, ...b, c) {
  // ...
}
```
> 注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错


### 箭头函数

**箭头函数与普通函数的区别**

调用this的区别,箭头函数没有this，this根据上下文来绑定

函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。


```	
			var obj={"name":"bgg"};
			function CreatePerson(){
				console.log(this)
			}
			
			CreatePerson.call(window); //相当于CreatePerson();  //window对象
			CreatePerson.call(obj);							//{"name":"bgg"}
			
```

```
			var obj={"name":"bgg"};
			var CreatePerson=()=>{console.log(this)}
			
			CreatePerson.call(window); //相当于CreatePerson();  //window对象
			CreatePerson.call(obj);							//window对象
```


**箭头无法当构造函数，使用new操作符来实例化对象。因为this在定义时已绑定了上下文，在调用函数时无法改变this.**

```
			var CreatePerson=(name,age)=>{
				this.name=name;
				this.age=age;
			}
			var p1=new CreatePerson("p1",18);
			console.log(p1);   //CreatePerson is not a constructor
```

**this的指向**
ul下有若干个li
```
class Card{
				constructor(li,div,style){
					this.li=li;
					this.div=div;
					this.style=style;
					this.li.forEach((x,index)=>{
						x.index=index;
						x.addEventListener("click",this.clicksty.bind(this)) 
												//this.clicksty 箭头函数，此时的this是指向实例化的对象。
												//clicksty.bind(this)把当前点击的li对象传过去
					})
				}
				
				clicksty(e){
					this.reset();
					console.log(e.target.index);
					e.target.classList.add(this.style);
					this.div[e.target.index].classList.add(this.style)
				}
}

			var li=document.querySelectorAll("li");
			var div=document.querySelectorAll(".content");
			
			
			var c=new Card(li,div,"dispaly_b");
```



**ES6 允许使用“箭头”（`=>`）定义函数。**

```
var f = v => v;

// 等同于
var f = function (v) {
  return v;
};
```
```
[1,2,3].forEach((e)=>{console.log(e)})  //注意前面代码要以';'结束

//同等于
[1,2,3].forEach(function(e){console.log(e)})
```

**如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。**
```
var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```

**如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用`return`语句返回。**
```
var sum = (num1, num2) => { return num1 + num2; }

var sum1 = () => {console.log(123)}
```

**由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。**
```
// 报错
let getTempItem = id => { id: id, name: "Temp" };

// 不报错
let getTempItem = id => ({ id: id, name: "Temp" });
```

**下面是一种特殊情况，虽然可以运行，但会得到错误的结果。**
```
let foo = () => { a: 1 };
foo() // undefined	
```

> 上面代码中，原始意图是返回一个对象{ a: 1 }，但是由于引擎认为大括号是代码块，所以执行了一行语句a: 1。    
这时，a可以被解释为语句的标签，因此实际执行的语句是1;，然后函数就结束了，没有返回值。

**如果箭头函数只有一行语句，且不需要返回值，可以采用下面的写法，就不用写大括号了。**
```
let fn = () => void doesNotReturn();
```
```
var aa=()=>{console.log("123"); return "aa"};

let bb=()=>void aa();
bb();
```

### 尾递归
函数调用自身，称为递归。如果尾调用自身，就称为尾递归。

例如3的阶乘：

**递归**
```
			function jc(n){
				if(n<=1){return 1}
				return jc(n-1)*n;
			}
			console.log(jc(3));
```

**尾递归**
```
			function jc1(n,total){
				if(n<=1){return total}
				return jc1(n-1,n*total);
			}
			console.log(jc1(3,1));
```

> 递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。    
但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。


## Set 和 Map 数据结构

### Set

* `add(value)`：添加某个值，返回 Set 结构本身。
* `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
* `has(value)`：返回一个布尔值，表示该值是否为Set的成员。
* `clear()`：清除所有成员，没有返回值。

ES6 提供了新的数据结构 `Set`。它类似于数组，但是`成员的值都是唯一的`，没有重复的值。

**去重**
```
			var set=new Set();
			var arr=[10,9,10,20,20,5];
			arr.forEach(x=>set.add(x));
			console.log(set)
```

> 通过`add`方法向 Set 结构加入成员

**Set 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。**
```
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size // 5

// 例三
const set = new Set(document.querySelectorAll('div'));
set.size // 56

// 类似于
const set = new Set();
document
 .querySelectorAll('div')
 .forEach(div => set.add(div));
set.size // 56
```

### Map

* size 属性

size属性返回 Map 结构的成员总数。

* set(key, value)

set方法设置键名key对应的键值为value，然后返回整个 Map 结构。

(set方法返回的是当前的Map对象，因此可以采用链式写法。)

* get(key)

get方法读取key对应的键值，如果找不到key，返回undefined。

* has(key)

has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。

* delete(key)

delete方法删除某个键，返回true。如果删除失败，返回false。

* clear()

clear方法清除所有成员，没有返回值。
传统上对象只能用字符串当作键。这给它的使用带来了很大的限制。

Map对象"键"的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

```
			var m=new Map();
			var o={"name":"bgg"};
			m.set(o,"一统江湖");
			console.log(m);
			console.log(m.get(o))
```


## Promise 对象
**Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。**

通过某种事件触发的函数、回调函数属于异步编程

IIFE( Immediately Invoked Function Expression) 立刻执行函数表达式

**`Promise`对象有以下两个特点。**

（1）对象的状态不受外界影响。   
`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。    
只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。    
这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。    
Promise对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。    
只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为` resolved`（已定型）。    
如果改变已经发生了，你再对Promise对象添加回调函数，也会立即得到这个结果。    
`这与事件（Event）完全不同`，事件（Event）的特点是，如果你错过了它，再去监听，是得不到结果的。


实例化promise对象
```js
const promise=new Promise(function(resolve,reject){
//异步造作成功时执行resolve,失败时执行reject
	if(true){
		resolve("value")
	}
	else{
		reject("error")
	}
});
promise.then(function(value){
	console.log("then",value)
},function(error){
	console.log("then",error)
})
```

> 扩展:    
扩展Promise.all：有三个异步必须都执行完后，才能执行第四个异步    
```Promise.all( [new Promise(func1), new Promise(func2), new Promise(func3)]).then(func4);```    
扩展：promise.race()：三个异步中有一个执行完之后就执行第四个    
```Promise.race( [new Promise(func1), new Promise(func2), new Promise(func3)]).then(func4);```

用Promise封装AJAX
```js
var obj={"methods":"GET","url":"https://www.easy-mock.com/mock/5bbb303e77507c466f451909/xianyufansheng7/getfile7"}
function ajax(ajaxobj){
	return new Promise(function(resolve,reject){
		var xhr=new XMLHttpRequest();
		xhr.open(ajaxobj.methods,ajaxobj.url);
		xhr.send();
		xhr.addEventListener("readystatechange",function(){
			if(xhr.readyState==4){   //if(xhr.readyState==4&&xhr.status==200)获取数据可能失败，因为发送请求时没有符合条件语句，直接else
				if(xhr.status==200){resolve(xhr.response);}
				else{
					reject("位置错误");
				}
			}
		})
	})
}

ajax(obj).then(function(ajaxstr){
	console.log("obj：",ajaxstr);
},function(error){
	console.log(error)
});
```

**封装mysql**

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
		//连接数据库
		connection.connect();
		
		//构建原生sql语句,传给query方法处理数据	
		connection.query(sql,function(err,result){
			if(err){console.log(err);return}
			
			var obj=JSON.stringify(result);
				obj=JSON.parse(obj);
			resolve(obj);
		})
		//关闭数据库
		connection.end()
	})
}


let sql="select * from user where username='rrr'";
getData(sql)
.then((res)=>{
	console.log(res)
})
```

**Promise.prototype.catch()**

Promise.prototype.catch方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。
```js
var i=true;
var p=new Promise(function(resolve,reject){
	if(!i){resolve("成功")}
	else{
//reject(Error("promise error"));
		throw Error("what?");
	}
	
})
.then(function(str){console.log(str)})
.catch(function(err){console.error(err)})
```
