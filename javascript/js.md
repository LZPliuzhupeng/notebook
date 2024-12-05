## 工厂模式

```js
function newobj(name,age){
	var obj={};
	obj.name=name;
	obj.age=age;
	return obj;
}
var ooo=newobj("liu",18);
console.log(ooo);

console.log( ooo instanceof Object);   //true	 判断ooo是否Object对象的实例
console.log( ooo instanceof newobj);   //false   判断ooo是否newobj类的实例
```

> 缺点： 无法识别构造函数

## 构造函数模式

```js
function CreatPerson(name,age,job){
	this.name=name;
	this.age=age;
	this.job=job;
	this.sayName=function(){
		alert(this.name);
	}
};

var ppp=new CreatPerson("秃头",18,"开发");
var qqq=new CreatPerson("地中海",20,"编程");
console.log(ppp instanceof CreatPerson);  //true
```

> 特点:    
	1.没有显示地创建一个对象    
	2.将属性和方法都赋值给this对象    
	3.没有使用return 关键字    
	4.使用new 关键字实例化对象    
缺点:    
	虽然解决了对象识别问题,但构造函数体内的方法不是来自同一个对象    

## 原型模型
每次命名一个构造函数,其含有prototype属性指向构造函数的原型对象,这个原型对象中有constrcutor属性指向构造函数.

```js
function yuanxing(name,age,job){
	this.name=name;
	this.age=age;
	this.job=job;
}

yuanxing.prototype.sayName=function(){
	alert(name);
}
var y1=new yuanxing("提莫",5,"射手");
var y2=new yuanxing("盖伦",5,"mt");

console.log(y1.sayName===y2.sayName);  //true
```
> 解决了"实例化对象中方法来自不同对象"的问题

## Object
+ [JS大杂烩](/javascript/JSbasics.md)

### Object.assign() 
 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。
 
 **复制一个对象**
 
 ```js
var obj = { a: 1 };
var copy = Object.assign({}, obj);
console.log(copy); // { a: 1 }
 ```

**合并对象**

```js
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };

var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 }, 注意目标对象自身也会改变。
```

**合并具有相同属性的对象**

```js
var o1 = { a: 1, b: 1, c: 1 };
var o2 = { b: 2, c: 2 };
var o3 = { c: 3 };

var obj = Object.assign({}, o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
```


## Array（数组对象）

```js
var arr=new Array("鸡蛋1","鸡蛋2","鸡蛋3");
var arr2=["鸡蛋1","鸡蛋2","鸡蛋3"];
var arr3=arr;				//引用与arr相同的对象
console.log(arr);
console.log(arr2);
console.log(arr==arr2);      //false(引用类型，引用不是同一个对象)
console.log(arr==arr3);	 //true
```

### join()（原数组未改变）
join(separator): 将数组的元素组起一个字符串，以separator为分隔符，省略的话则用默认用逗号为分隔符，该方法只接收一个参数：即分隔符。
```js
var arr = [1,2,3];
console.log(arr.join()); // 1,2,3
console.log(arr.join("-")); // 1-2-3
console.log(arr); // [1, 2, 3]（原数组不变）
```

通过join()方法可以实现重复字符串，只需传入字符串以及重复的次数，就能返回重复后的字符串，函数如下：    
```js
function repeatString(str, n) {
return new Array(n + 1).join(str);
}
console.log(repeatString("abc", 3)); // abcabcabc
console.log(repeatString("Hi", 5)); // HiHiHiHiHi
```

### push()和pop()
push(): 可以接收任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度。 

pop()：数组末尾移除最后一项，减少数组的 length 值，然后返回移除的项。
```js
var arr = ["Lily","lucy","Tom"];
var count = arr.push("Jack","Sean");
console.log(count); // 5
console.log(arr); // ["Lily", "lucy", "Tom", "Jack", "Sean"]
var item = arr.pop();
console.log(item); // Sean
console.log(arr); // ["Lily", "lucy", "Tom", "Jack"]
```

### unshift()和shift()

unshift:将参数添加到原数组开头，并返回数组的长度 。 
  
shift()：删除原数组第一项，并返回删除元素的值；如果数组为空则返回undefined 。    

这组方法和上面的push()和pop()方法正好对应，一个是操作数组的开头，一个是操作数组的结尾。    
```js
var arr = ["Lily","lucy","Tom"];
var count = arr.unshift("Jack","Sean");
console.log(count); // 5
console.log(arr); //["Jack", "Sean", "Lily", "lucy", "Tom"]
var item = arr.shift();
console.log(item); // Jack
console.log(arr); // ["Sean", "Lily", "lucy", "Tom"]
```

### sort()
sort()：按升序排列数组项——即最小的值位于最前面，最大的值排在最后面。 
   
在排序时，sort()方法会调用每个数组项的 toString()转型方法，然后比较得到的字符串，以确定如何排序。
    
即使数组中的每一项都是数值， sort()方法比较的也是字符串，因此会出现以下的这种情况：    
```js
var arr1 = ["a", "d", "c", "b"];
console.log(arr1.sort()); // ["a", "b", "c", "d"]
arr2 = [13, 24, 51, 3];
console.log(arr2.sort()); // [13, 24, 3, 51]
console.log(arr2); // [13, 24, 3, 51](元数组被改变)
```
为了解决上述问题，sort()方法可以接收一个比较函数作为参数，以便我们指定哪个值位于哪个值的前面。
    
比较函数接收两个参数，如果第一个参数应该位于第二个之前则返回一个负数，如果两个参数相等则返回 0，如果第一个参数应该位于第二个之后则返回一个正数。   
`即返回正式则调换位置，非正数不调换` 

以下就是一个简单的比较函数：
```js
function compare(value1, value2) {
	if (value1 < value2) {
		return -1;
	} else if (value1 > value2) {
		return 1;
	} else {
		return 0;
	}
}
arr2 = [13, 24, 51, 3];
console.log(arr2.sort(compare)); // [3, 13, 24, 51]
```

如果需要通过比较函数产生降序排序的结果，只要交换比较函数返回的值即可：    
```js
function compare(value1, value2) {
	if (value1 < value2) {
		return 1;
	} else if (value1 > value2) {
		return -1;
	} else {
		return 0;
	}
}
arr2 = [13, 24, 51, 3];
console.log(arr2.sort(compare)); // [51, 24, 13, 3]
```

### reverse()
reverse()：反转数组项的顺序。    
```js
var arr = [13, 24, 51, 3];
console.log(arr.reverse()); //[3, 51, 24, 13]
console.log(arr); //[3, 51, 24, 13](原数组改变)
```

### concat()（原数组未改变）（返回添加）
concat() ：将参数添加到原数组中。   

这个方法会先创建当前数组一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。   

在没有给 concat()方法传递参数的情况下，它只是复制当前数组并返回副本。
```js
var arr = [1,3,5,7];
var arrCopy = arr.concat(9,[11,13]);
console.log(arrCopy); //[1, 3, 5, 7, 9, 11, 13]
console.log(arr); // [1, 3, 5, 7](原数组未被修改)
```
从上面测试结果可以发现：传入的不是数组，则直接把参数添加到数组后面， 	  
如果传入的是数组，则将数组中的各个项添加到数组中。但是如果传入的是一个二维数组呢？
 ```js
 var arrCopy2 = arr.concat([9,[11,13]]);
console.log(arrCopy2); //[1, 3, 5, 7, 9, Array[2]]
console.log(arrCopy2[5]); //[11, 13]
```
上述代码中，arrCopy2数组的第五项是一个包含两项的数组，也就是说concat方法只能将传入数组中的每一项添加到数组中，    
如果传入数组中有些项是数组，那么也会把这一数组项当作一项添加到arrCopy2中。    

### slice()（原数组未改变）（返回选取）
* slice()：返回从原数组中指定开始下标到结束下标之间的项组成的新数组。
    
slice()方法可以接受一或两个参数，即要返回项的起始和结束位置。在只有一个参数的情况下， slice()方法返回从该参数指定位置开始到当前数组末尾的所有项。    

如果有两个参数，该方法返回起始和结束位置之间的项——但不包括结束位置的项。
```js
var arr = [1,3,5,7,9,11];
var arrCopy = arr.slice(1);
var arrCopy2 = arr.slice(1,4);
var arrCopy3 = arr.slice(1,-2);
var arrCopy4 = arr.slice(-4,-1);
console.log(arr); //[1, 3, 5, 7, 9, 11](原数组没变)


console.log(arrCopy); //[3, 5, 7, 9, 11]
console.log(arrCopy2); //[3, 5, 7]
console.log(arrCopy3); //[3, 5, 7]
console.log(arrCopy4); //[5, 7, 9]
```
arrCopy只设置了一个参数，也就是起始下标为1，所以返回的数组为下标1（包括下标1）开始到数组最后。		 

arrCopy2设置了两个参数，返回起始下标（包括1）开始到终止下标（不包括4）的子数组。 		

<span>arrCopy3设置了两个参数，终止下标为负数，当出现负数时，将负数加上数组长度的值（6）来替换该位置的数，因此就是从1开始到4（不包括）的子数组。	</span>	 

</span>arrCopy4中两个参数都是负数，所以都加上数组长度6转换成正数，因此相当于slice(2,5)。		</span>

### splice()
* splice()：很强大的数组方法，它有很多种用法，可以实现删除、插入和替换。    
删除：可以删除任意数量的项，只需指定 2 个参数：要删除的第一项的位置和要删除的项数。例如， splice(0,2)会删除数组中的前两项。

插入：可以向指定位置插入任意数量的项，只需提供 3 个参数：起始位置、 0（要删除的项数）和要插入的项。例如，splice(2,0,4,6)会从当前数组的位置 2 开始插入4和6。

替换：可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需指定 3 个参数：起始位置、要删除的项数和要插入的任意数量的项。
插入的项数不必与删除的项数相等。 
   
例如，splice (2,1,4,6)会删除当前数组位置 2 的项，然后再从位置 2 开始插入4和6。

splice()方法始终都会返回一个数组，该数组中包含从原始数组中删除的项，如果没有删除任何项，则返回一个空数组。
```js
var arr=new Array("鸡蛋0", "鸡蛋1", "鸡蛋2", "鸡蛋3", "鸡蛋4");
arr.splice(1,2); 					//  ["鸡蛋0", "鸡蛋3", "鸡蛋4"]  (从下标1位置开始删除两个项)
arr.splice(1,0,"鸡蛋清");			//	["鸡蛋0", "鸡蛋清", "鸡蛋1", "鸡蛋2", "鸡蛋3", "鸡蛋4"] (从下标1位置开始,不删除项，直接插入)
arr.splice(1,1,"替换鸡蛋1","我也替换鸡蛋1"); // ["鸡蛋0", "替换鸡蛋1", "我也替换鸡蛋1", "鸡蛋2", "鸡蛋3", "鸡蛋4"] (从下标1位置开始,删除一个项项，插入)
console.log(arr);	
```

### indexOf()和 lastIndexOf()
* indexOf()：接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中， 从数组的开头（位置 0）开始向后查找。 

* lastIndexOf：接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中， 从数组的末尾开始向前查找。

这两个方法都返回要查找的项在数组中的位置，或者在没找到的情况下返回1。在比较第一个参数与数组中的每一项时，会使用全等操作符。
```js
var arr = [1,3,5,7,7,5,3,1];
console.log(arr.indexOf(5)); //2
console.log(arr.lastIndexOf(5)); //5
console.log(arr.indexOf(5,2)); //2
console.log(arr.lastIndexOf(5,4)); //2
console.log(arr.indexOf("5")); //-1
```

### forEach()
* forEach()：对数组进行遍历循环，对数组中的每一项运行给定函数。这个方法没有返回值。参数都是function类型，默认有传参，参数分别为：遍历的数组内容；对应的数组索引，数组本身。

```js
var arr = [1, 2, 3, 4, 5];
arr.forEach(function(x, index, a){
	console.log(x + '|' + index + '|' + (a === arr));
});
// 输出为：
// 1|0|true
// 2|1|true
// 3|2|true
// 4|3|true
// 5|4|true
```
> 改遍历循环不能跳出，只能跳出当前循环。而且不允许有返回值。        
<span>IOS8不支持forEach方法</span>

### map()
* map()：指"映射"，对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。

下面代码利用map方法实现数组中每个数求平方。
```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.map(function(item){
	return item*item;
});
console.log(arr2); //[1, 4, 9, 16, 25]
```

### filter()
* filter()："过滤"功能，数组中的每一项运行给定函数，返回满足过滤条件组成的数组。

```js
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
var arr2 = arr.filter(function(x, index) {
	return index % 3 === 0 || x >= 8;
}); 
console.log(arr2); //[1, 4, 7, 8, 9, 10]
```

### every()
* every()：判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。

```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.every(function(x) {
	return x < 10;
}); 
console.log(arr2); //true
var arr3 = arr.every(function(x) {
	return x < 3;
}); 
console.log(arr3); // false
```


### some()
* some()：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。

```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.some(function(x) {
	return x < 3;
}); 
console.log(arr2); //true
var arr3 = arr.some(function(x) {
	return x < 1;
}); 
console.log(arr3); // false
```


### reduce()和 reduceRight()
这两个方法都会实现迭代数组的所有项，然后构建一个最终返回的值。reduce()方法从数组的第一项开始，逐个遍历到最后。而 reduceRight()则从数组的最后一项开始，向前遍历到第一项。

这两个方法都接收两个参数：一个在每一项上调用的函数和（可选的）作为归并基础的初始值。

传给 reduce()和 reduceRight()的函数接收 4 个参数：前一个值、当前值、项的索引和数组对象。这个函数返回的任何值都会作为第一个参数自动传给下一项。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数就是数组的第二项。

下面代码用reduce()实现数组求和，数组一开始加了一个初始值10。
```js
var values = [1,2,3,4,5];
var sum = values.reduceRight(function(prev, cur, index, array){
	return prev + cur;
},10);
console.log(sum); //25
```

### Array.length
数组的length属性总是比数组中定义的最后一个元素的下标大一。对于那些具有连续元素，而且以元素0开始的常规数组来说，属性length声明了数组中的元素个数。 

数组的length属性在用构造函数Array()创建数组时初始化。给数组添加新元素时，如果必要，将更新length的值：
```js
a = new Array(  );                       // a.length 被初始化为 0

b = new Array(10);                       // b.length 被初始化为 10

c = new Array("one", "two", "three");    // c.length 被初始化为 3

c[3] = "four";                           // c.length 被更新为 4

c[10] = "blastoff";                      // c.length 变为 11 
```
设置属性length的值可以改变数组的大小。如果设置的值比它的当前值小，数组将被截断，其尾部的元素将丢失。

如果设置的值比它的当前值大，数组将增大，新元素被添加到数组尾部，它们的值为undefined。

### Array.toLocaleString( ) 
* 数组array的局部字符串表示。


`把数组转换成局部字符串 `

`覆盖 Object.toLocaleString( ) `

描述      
数组的方法toString()将返回数组的局部字符串表示。    
它首先调用每个数组元素的toLocaleString()方法，然后用地区特定的分隔符把生成的字符串连接起来，形成一个字符串。

> 主意：调用该方法时，如果对象不是Array，则抛出异常。(TypeError )

### includes()
判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 true，否则返回false。

* `arr.includes(searchElement)`
* `arr.includes(searchElement, fromIndex)`

**参数**

searchElement

需要查找的元素值。

fromIndex 可选

从该索引处开始查找 searchElement。如果为负值，则按升序从 array.length + fromIndex 的索引开始搜索。
默认为 0。

```js
[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
[1, 2, NaN].includes(NaN); // true
```

### fill()
用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引。

```js
var array1 = [1, 2, 3, 4];

console.log(array1.fill(0, 2, 4));  //[1, 2, 0, 0]

console.log(array1.fill(5, 1));  //[1, 5, 5, 5]

console.log(array1.fill(6));   [6, 6, 6, 6]
```

### flat()
方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

`var newArray = arr.flat(depth)`

```js
var arr1 = [1, 2, [3, 4]];
arr1.flat(); 
// [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();
// [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]

//使用 Infinity 作为深度，展开任意深度的嵌套数组
arr3.flat(Infinity); 
// [1, 2, 3, 4, 5, 6]
```

```js
var arr4 = [1, 2, , 4, 5];
arr4.flat();
// [1, 2, 4, 5]
//null 和 undefined不会删除
```


## String（字符串对象）

```
var str="abc";
var str01=new String("aabbcc");
```

+ String.charAt( ) 返回字符串中的第n个字符 
+ String.charCodeAt( ) 返回字符串中的第n个字符的代码 
+ String.concat( ) 连接字符串 
+ String.fromCharCode( ) 从字符编码创建—个字符串 
+ String.indexOf( ) 检索字符串 
+ String.lastIndexOf( ) 从后向前检索一个字符串 
+ String.length 字符串的长度 
+ String.localeCompare( ) 用本地特定的顺序来比较两个字符串 
+ String.match( ) 找到一个或多个正则表达式的匹配 
+ String.replace( ) 替换一个与正则表达式匹配的子串 
+ String.search( ) 检索与正则表达式相匹配的子串 
+ String.slice( ) 抽取一个子串 
+ String.split( ) 将字符串分割成字符串数组 
+ String.substr( ) 抽取一个子串 
+ String.substring( ) 返回字符串的一个子串 
+ String.toLocaleLowerCase( ) 把字符串转换小写 
+ String.toLocaleUpperCase( ) 将字符串转换成大写 
+ String.toLowerCase( ) 将字符串转换成小写 
+ String.toString( ) 返回字符串 
+ String.toUpperCase( ) 将字符串转换成大写 
+ String.valueOf( ) 返回字符串 


### charAt()
返回字符串中的第n个字符

* `string.charAt(n)`
返回字符串string的第n个字符

**返回值**

方法String.charAt()返回字符串string中的第n个字符。字符串中第一个字符的下标值是0。

如果参数n不在0和string.length-1之间，该方法将返回一个`空字符串`。

> 注意，JavaScript并没有一种有异于字符串类型的字符数据类型，所以返回的字符是长度为1的字符串。


### charCodeAt( )
返回字符串中的第n个字符的代码

* `string.charCodeAt(n)`
返回string中的第n个字符的Unicode编码。这个返回值是0～65535之间的16位整数。

**返回值**

方法charCodeAt()与charAt()执行的操作相似，只不过前者返回的是位于指定位置的字符的编码，而后者返回的则是含有字符本身的子串。

如果n是负数，或者大于等于字符串的长度，则charCodeAt()返回`NaN`。


### concat( )
连接字符串

* `string.concat(value, ...) `
把每个参数都连接到字符串string上得到的新字符串。

**返回值**

方法concat()将把它的所有参数都转换成字符串(如果必要)，然后按顺序连接到字符串string的尾部，返回连接后的字符串。

> 注意，string自身并没有被修改。     
String.concat()与Array.concat()很相似。    
注意，使用"+"运算符来进行字符串的连接运算通常更简便一些。    
`str=str1+str2;`

### fromCharCode( )
从字符编码创建—个字符串

* `String.fromCharCode(c1, c2, ...) `
零个或多个整数，声明了要创建的字符串中的字符的Unicode编码。

这个静态方法提供了一种创建字符串的方式，即字符串中的每个字符都由单独的数字 Unicode编码指定。
>　注意，作为—种静态方法，fromcharCode()是构造函数 String()的属性，而不是字符串或String对象的方法。

### indexOf( )
检索字符串

* `string.indexOf(substring)`
* ` string.indexOf(substring,start)`

`substring `
要在字符串string中检索的子串。

`start `
一个可选的整数参数，声明了在字符串String中开始检索的位置。
它的合法取值是0(字符串中的第一个字符的位置)到string.length-1(字符串中的最后一个字符的位置)。
如果省略了这个参数，将从字符串的<span>第一个字符</span>开始检索。

**返回值**

如果在string中的start位置之后存在substring返回出现的第一个substring 的位置。如果没有找到子串substring返回-1。

```js
var str01=new String("aabbcc");
console.log(str01.indexOf("c"));  //4
console.log(str01.lastIndexOf("c"));  //5
```
> 说明：不同之处只是在头部还是尾部开始检索，    
上面例子indexOf在头部开始检索返回的是下标为4的"c"，    
lastIndexOf在尾部开始检索返回的是下标为5的"c"。


描述
<em>
方法string.indexOf()将<span>从头到尾的检索</span>字符串string，看它是否含有子串 substring。
开始检索的位置在字符串string的start处或string的开头(没有 指定start参数时)。
如果找到了一个substring那么String.indexOf()将返回 substring的第一个字符在string中的位置。
string中的字符位置是从0开始的。 
</em>

### lastIndexOf( )
从后向前检索一个字符串


* `string.lastIndexOf(substring) `

* `string.lastIndexOf(substring, start)`

`substring `
要在字符串string中检索的子串。

`start `
一个可选的整数参数，声明了在字符串string中开始检索的位置。
它的合法取值是0(字符串中的第一个字符的位置)到string.1ength-1(字符串中的最后一个字符的位置)。
如果省略了这个参数，将从字符串的<span>最后一个字符</span>处开始检索。

**返回值**

如果在string中的start位置之前存在substring那么返回的就是出现的最后一个substring的位置。
如果没有找到子串substring那么返回的是-1。

描述

<em>方法String.1astIndexOf()将<span>从尾到头的检索</span>字符串string看它是否含有子串 substring。
开始检索的位置在字符串string的start处或string的结尾(没有 指定start参数时)。
如果找到了一个substring，那么String.lastIndexOf()将返回substring的第一个字符在string中的位置。
由于是从尾到头的检索一个字符串，所以找到的第一个substrlng其实是strlng中出现在位置start之前的最后一个substring。
</em>


### length
字符串的长度

* `string.length `

描述

<em>
属性String.1ength是一个只读整数，它声明了指定字符串string中的字符数。
对于任何一个字符串s，它最后一个字符的下标都是s.1ength-1。用for/in循环不能枚举出字符串的length属性，用delete运算符也不能删除它。
</em>

### localeCompare( )
用本地特定的顺序来比较两个字符串

* `string.localeCompare(target)`

`target `

要以本地特定的顺序与string进行比较的字符串。

**返回值**

说明比较结果的数字。
如果string小于target，则localeCompare()返回小于0的数。
如果string大干target，该方法返回大于0的数。如果两个字符串相等，或根据本地排序规约没有区别，该方法返回0。 

描述

<em>
把<和>运算符应用到字符串时，它们只用<span>字符的Unicode编码比较字符串</span>，而不考虑当地的排序规约。
以这种方法生成的顺序不一定是正确的。
例如，西班牙语中，其中字母"ch"通常作为出现在字母"c"和"d"之间的字符来排序。 
localeCompare()方法提供的比较字符串的方法，考虑了默认的本地排序规约。
 ECMAScript标准没有规定如何进行本地特定的比较操作，它只规定该函数采用底层 操作系统提供的排序规约。 
</em>

### match( )
找到一个或多个正则表达式的匹配

* `string.match(regexp)`

`regexp `

声明了要匹配的模式的RegExp对象。
如果该参数不是RegExp对象，则首先将把它传递给RegExp()构造函数，把它转换成RegExp对象。

**返回值**

存放匹配结果的数组。该数组的内容依赖于regexp是否具有全局性质g。下面详细说明了这个返回值。

描述

<em>
方法match()将检索字符串string，以找到一个或多个与regexp匹配的文本。这个方法的行为很大程度上依赖于regexp是否具有性质g。

如果regexp没有性质g，那么match()就只能在string中执行一次匹配，如果没有找到任何匹配的文本，match()将返回null。否则，它将返回一个数组，其中存放了与它找到的匹配文本有关的信息。该数组的第0个元素存放的是匹配文本，其余的元素存放的是与正则表达式的子表达式匹配的文本。除了这些常规的数组元素之外，返回的数组还含有两个对象属性。index属性声明的是匹配文本的起始字符在string中的位置，input属性声明的是对string的引用。 

如果regexp具有标志g，那么match()将执行全局检索，找到string中的所有匹配子串。如果没有找到任何匹配的子串。它将返回null。如果找到了一个或多个匹配子串，它将返回一个数组。不过，全局匹配返回的数组的内容与前者大不相同，它的数组元素存放的是string中的所有匹配子串，而且它也没有index属性和input属性。注意，在全局匹配的模式下，match()既不提供与子表达式匹配的文本的信息，也不声明每个匹配子串的位置。如果你需要这些全局检索的信息，可以使用RegExp.exec()。
</em>

### replace( )
替换一个与正则表达式匹配的子串

* `string.replace(regexp, replacement)`

`regexp` 

声明了要替换的模式的RegExp对象。
如果该参数是一个字符串，则将它作为要检索的直接量文本模式，而不是首先被转换成RegExp对象。

`replacement `

一个字符串，声明的是替换文本或生成替换文本的函数。详见描述部分。

**返回值**

一个新字符串，是用replacemenc替换了与regexp的第一次匹配或所有匹配之后得到的。

```js
var str01=new String("aabbcc");
console.log(str01.replace("c","1")); //aabb1c
```

正则表达式匹配(a到z的转换成大写，g为全局匹配，如不声明默认情况下匹配到一个符合条件的会停止)
```js
var str01=new String("aa11bbcc");
var strto=str01.replace(/[a-z]/g,function(a){    //在上面写死的数值变为用函数的返回值
	return a.toUpperCase()
})
console.log(strto);
```


描述

<em>
字符串string的方法replace()执行的是查找并替换的操作。
它将在string中查找与regexp相匹配的子串，然后用replacement替换这些子串。
如果regexp具有全局性质g，那么replace()将替换所有的匹配子串。否则，它只替换第一个匹配子串。

replacement可能是字符串或函数。如果它是一个字符串，那么每个匹配都将由字符 串替换。
但replacement中的$字符具有特殊的含义。如下表所示，它说明从模式匹配得到的字符串将用于替换。
</em>	


### search( )
检索与正则表达式相匹配的子串

* `string.search(regexp)`

`regexp `

要在字符串string中检索的RegExp对象，该对象具有指定的模式。
如果该参数不是RegExp对象，则首先将它传递给RegExp()构造函数，把它转换成 RegExp对象。

**返回值**

返回string中第一个与regexp相匹配的子串的起始位置。如果没有找到任何匹配的子 串，则返回-1。

描述
<em>
方法search()将在字符串string中检索与regexp相匹配的子串，并且返回第一个匹配子串的第一个字符的位置。
如果没有找到任何匹配的子串，则返回-1。

search()并不执行全局匹配，它将忽略标志g。
它也忽略regexp的lastIndex属性，并且总是从字符串的开始进行检索，这意味着它总是返回string的第一个匹配的位置。
</em>

### slice( )
抽取一个子串

* `string.slice(start, end)`

`start `
要抽取的片段的起始下标。如果是负数，那么该参数声明了从字符串的尾部开始算起的位置。也就是说，-1指字符串中的最后一个字符，-2指倒数第二个字符，以此类推。

`end` 
紧接着要抽取的片段的结尾的下标。如果没有指定这一参数，那么要抽取的子串包括start到原字符串结尾的字符串。如果该参数是负数，那么它声明了从字符串的尾部开始算起的位置

**返回值**

一个新字符串，包括字符串string从start开始(包括start)到end为止(不包 括end)的所有字符

```js
var str_01="and in it he says Any damn fool could";
var a_01=str_01.slice(18);
var b_01=str_01.slice(0,3);
var c_01=str_01.slice(-6);
console.log("a_01="+a_01);  //a_01=Any damn fool could
console.log("b_01="+b_01);  //b_01=and
console.log("c_01="+c_01)  //c_01= could
```

描述
<em>
方法slice()将返回一个含有字符串string的片段的字符串或返回它的一个子串。 但是该方法不修改string。

String对象的方法slice()、substring()和substr()(不建议使用)都返回字符串的指定部分。
slice()比substring()要灵活一些，因为它允许使用负数作为参数。
slice()与substr()有所不同， 因为它用两个字符的位置指定子串，而substr()则用字符位置和长度来指定子串。

> 还要注意的是，<span>String.slice()与Array.slice()</span>相似。
</em>

### split( )
将字符串分割成字符串数组

* `string.split(delimiter, limit)`

`delimiter `

字符串或正则表达式，从该参数指定的地方分割string。
ECMAScript V3标准化了正则表达式作为定界符使用时的用法，JavaScript 1.2和JScript 3.0实现了它。
JavaScriptl.1没有实现它。

`limit `

这个可选的整数指定了返回的数组的最大长度。如果设置了该参数，返回的子串不会多于这个参数指定的数字。
如果没有设置该参数，整个字符串都会被分割，不考虑它的长度。

> ECMAScriptv3标准化了该参数，JavaScript 1.2和JScript 3.0实现了它。JavaScript 1.1没有实现它。

**返回值**

一个字符串数组，是通过在delimiter指定的边界处将字符串string分割成子串创建的。
返回的数组中的子串不包括delimiter自身，但下面列出的情况除外。

```js
var str01=new String("aabbcc");
console.log(str01.split("b"));  //(3) ["aa", "", "cc"]  (两个b中间会分割一个空的字符串)
console.log(str01.split("b"));  //(2) ["aa", "cc"]
```


描述
<em>
方法split()将创建并返回一个字符串数组，该数组中的元素是指定的字符串string 的子串，最多具有limit个。
这些子串是通过从头到尾检索字符串中与delimiter 匹配的文本，在匹配文本之前和之后分割string得到的。
返回的子串中不包括定界符 文本(下面提到的情况除外)。
如果定界符从字符串开头开始匹配，返回的数组的第一个元素是空串，即出现在定界符之前的文本。
同样，如果定界符与字符串的结尾匹 配，返回的数组的最后一个元素也是空串(假定与limit没有冲突)。

如果没有指定delimiter，那么它根本就不对string执行分割，返回的数组中只有一个元素，而不分割字符串元素。
如果delimiter是一个空串或与空串匹配的正则表 达式，那么string中的每个字符之间都会被分割，返回的数组的长度与字符串长度相 等(假定王imic不小于该长度)。
> (注意，这是一种特殊情况，因为没有匹配第一个字符之前和最后一个字符之后的空串。)

前面说过，该方法返回的数组中的子串不包括用于分割字符串的定界符文本。
但如果delimiter是包括子表达式的正则表达式，那么返回的数组中包括与这些子表达式匹 配的子串(但不包括与整个正则表达式匹配的文本)。
</em>

> 注意，String.split()执行的操作与Array.join()执行的操作相反。

### substr( )
抽取一个子串
* `string.substr(start, length)	`

`start `

要抽取的子串的起始下标。如果是一个负数，那么该参数声明从字符串的尾部开始算起的位置。也就是说，-1指字符串中的最后—个字符，-2指倒数第二个字符，以此类推。

`length `

子串中的字符数。如果省略了这个参数，那么返回从string的开始位置到结尾的子串。

**返回值**

一个字符串的副本，包括从string的start处(包括start所指的字符)开始的1ength个字符。如果没有指定length，返回的字符串包含从start到string结尾的字符。

描述

<em>
substr()将在string中抽取并返回一个子串。但是它并不修改string。

> 注意，substr()指定的是子串的开始位置和长度，它是String.substring()和String.splice()的一种有用的替代方法，后两者指定的都是起始字符的位置。
但要注意，<span>ECMAScript没有标准化该方法</span>，因此反对使用它。
</em>

### substring( )
返回字符串的一个子串

* `string.substring(from, to)`

`from `

一个整数，声明了要抽取的子串的第一个字符在string中的位置。

`to` 

一个可选的整数，比要抽取的子串的最后一个字符在string中的位置多1。如果省略了该参数，返回的子串直到字符串的结尾。

**返回值**

一个新字符串，其长度为to-from，存放的是字符串string的一个子串。
这个新字符串含有的字符是从string中的from处到to-1处复制的。

描述

<em>
String.substring()将返回字符串string的子串，由from到to之间的字符构成， 包括位于from的字符，不包括位于to的字符。

如果参数from与to相等，那么该方法返回的就是一个空串(即长度为0的字符串)。
如果from比to大，那么该方法在抽取子串之前会先交换这两个参数。

要记住，<span>该子串包括from处的字符，不包括to处的字符</span>。虽然这样看来有违直觉， 但这种系统一个值得注意重要特性是，<span>返回的子串的长度总等于to-from</span>。
</em>

> Bug    
在JavaScript的Netscape实现中，如果明确地把语言版本设置为1.2(如把&lt;script&gt;标记的language性质设置为1.2)，那么如果from比to大，该方法不能正确地交换它的参数，而是返回空串。

### toLocaleLowerCase( )
把字符串转换小写

* `string.toLocaleLowerCase( )`

**返回值**

string的一个副本，按照本地方式转换成小写字母。只有几种语言(如土耳其语)具有地方特有的大小写映射，
所以该方法的返回值通常与<span>toLowerCase()</span>一样。

### toLocaleUpperCase( )
将字符串转换成大写

* `string.toLocaleUpperCase( )`

**返回值**

string的一个副本，按照本地方式转换成了大写字母。只有几种语言(如土耳其语) 具有地方特有的大小写映射，
所以该方法的返回值通常与<span>toUpperCase()</span>一样。

### toString( )
返回字符串

* `string.toString(  ) `

**返回值**

string的原始字符串值。一般不会调用该方法。
> **抛出**
TypeError     
调用该方法的对象不是String时抛出该异常。

### valueOf( )
返回字符串

* `string.valueOf( )`

**返回值**

string的原始字符串值。

> **抛出**
TypeError     
调用该方法的对象不是String时抛出该异常。

### trim() 
方法会从一个字符串的两端删除空白字符

* `str.trim()`

**trim() 方法并不影响原字符串本身，它返回的是一个新的字符串。**

```js
var orig = '   foo  ';
console.log(orig.trim()); // 'foo'

// 另一个.trim()例子，只从一边删除

var orig = 'foo    ';
console.log(orig.trim()); // 'foo'
```

> 兼容旧环境:    
```js
if (!String.prototype.trim) {
  String.prototype.trim = function () {
    return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '');
  };
}
```

## 	Date
操作日期和时间的对象

```js
//			new 操作符
//			实例化一个对象需要new操作符
//			如果不使用new的话,得到的是函数的返回值,而不是一个新的对象
			
var data=new Date();  //
var d=Date();
console.log(typeof data); //object
console.log(typeof d);   // string
```

+ Date.getDate( ) 返回一个月中的某一天 
+ Date.getDay( ) 返回一周中的某一天 
+ Date.getFullYear( ) 返回Date对象的年份字段 
+ Date.getHours( ) 返回Date对象的小时字段 
+ Date.getMilliseconds( ) 返回Date对象的毫秒字段 
+ Date.getMinutes( ) 返回Date对象的分钟字段 
+ Date.getMonth( ) 返回Date对象的月份字段 
+ Date.getSeconds( ) 返回Date对象的秒字段 
+ Date.getTime( ) 返回Date对象的毫秒表示 
+ Date.getTimezoneOffset( ) 判断与GMT的时间差 
+ Date.getUTCDate( ) 返回该天是一个月的哪一天(世界时) 
+ Date.getUTCDay( ) 返回该天是星期几(世界时) 
+ Date.getUTCFullYear( ) 返回年份(世界时) 
+ Date.getUTCHours( ) 返回Date对象的小时字段(世界时) 
+ Date.getUTCMilliseconds( ) 返回Date对象的毫秒字段(世界时) 
+ Date.getUTCMinutes( ) 返回Date对象的分钟字段(世界时) 
+ Date.getUTCMonth( ) 返回Date对象的月份(世界时) 
+ Date.getUTCSeconds( ) 返回Date对象的秒字段(世界时) 
+ Date.getYear( ) 返回Date对象的年份字段(世界时) 
+ Date.parse( ) 解析日期/时间字符串 
+ Date.setDate( ) 设置一个月的某一天 
+ Date.setFullYear( ) 设置年份，也可以设置月份和天 
+ Date.setHours( ) 设置Date对象的小时字段、分钟字段、秒字段和毫秒字段 
+ Date.setMilliseconds( ) 设置Date对象的毫秒字段 
+ Date.setMinutes( ) 设置Date对象的分钟字段和秒字段 
+ Date.setMonth( ) 设置Date对象的月份字段和天字段 
+ Date.setSeconds( ) 设置Date对象的秒字段和毫秒字段 
+ Date.setTime( ) 以毫秒设置Date对象 
+ Date.setUTCDate( ) 设置一个月中的某一天(世界时) 
+ Date.setUTCFullYear( ) 设置年份、月份和天(世界时) 
+ Date.setUTCHours( ) 设置Date对象的小时字段、分钟字段、秒字段和毫秒字段(世界时) 
+ Date.setUTCMilliseconds( ) 设置Date对象的毫秒字段(世界时) 
+ Date.setUTCMinutes( ) 设置Date对象的分钟字段和秒字段(世界时) 
+ Date.setUTCMonth( ) 设置Date对象的月份字段和天数字段(世界时) 
+ Date.setUTCSeconds( ) 设置Date对象的秒字段和毫秒字段(世界时) 
+ Date.setYear( ) 设置Date对象的年份字段 
+ Date.toDateString( ) 返回Date对象日期部分作为字符串 
+ Date.toGMTString( ) 将Date转换为世界时字符串 
+ Date.toLocaleDateString( ) 回Date对象的日期部分作为本地已格式化的字符串 
+ Date.toLocaleString( ) 将Date转换为本地已格式化的字符串 
+ Date.toLocaleTimeString( ) 返回Date对象的时间部分作为本地已格式化的字符串 
+ Date.toString( ) 将Date转换为字符串 
+ Date.toTimeString( ) 返回Date对象日期部分作为字符串 
+ Date.toUTCString( ) 将Date转换为字符串(世界时) 
+ Date.UTC( ) 将Date规范转换成毫秒数 
+ Date.valueOf( ) 将Date转换成毫秒表示 





### getDate( )
返回一个月中的某一天（获得多少号）

* `date.getDate(  )`

**返回值**

指定Date对象dace所指的月份中的某一天，使用本地时间。返回值是1～31之间的 一个整数。

### getDay( )
返回一周中的某一天(获得星期几)

* `date.getDay( )`

**返回值**

指定Date对象date所指的一个星期中的某一天，使用本地时间。返回值是0(周日) 到6(周六)之间的一个整数。

### getFullYear( )
返回年份

* `date.getFullYear(  )`

**返回值**

当dace用本地时间表示时返回的年份。返回值是一个四位数，表示包括世纪值在内的完整年份，而不是两位数的缩写形式。

### getHours( )
返回Date对象的小时字段

* `date.getHours( )`

**返回值**

指定Date对象date的小时字段，以本地时间表示。返回值是0(午夜)到23(晚上 11点)之间的一个整数。

### getMilliseconds( )
返回Date对象的毫秒字段

* `date.getMilliseconds(  )`

**返回值**

指定Date对象date的毫秒字段，用本地时间表示。返回值在0～999之间。

### getMinutes( )
返回Date对象的分钟字段

* `date.getMinutes( )`

**返回值**

指定的Date对象date的分钟字段，以本地时间表示。返回值在0～59之间。 

### getMonth( )	
返回Date对象的月份字段

* `date.getMonth( )`

**返回值**

指定的Date对象date的月份字段，以本地时间表示。返回值在0(一月)到11(十二月)之间。

### getSeconds( )
返回Date对象的秒字段

* `date.getSeconds( )`

**返回值**

指定的Date对象date的秒字段，以本地时间表示。返回值在0～59之间。 

### getTime( )
返回Date对象的毫秒表示

* `date.getTime( )`

**返回值**

指定的Date对象date的毫秒表示，也就是date指定的日期和时间距1970年1月1 日午夜(GMT时间)之间的毫秒数

描述
<em>
方法getTime()可以将日期和时间转换戍一个整数。
这在比较两个Date对象或者要判断两个日期之间的时差时非常有用。
> 注意，日期的毫秒表示独立于时区，所以除了这个方法外，没有getUTCTime()方法。    
不要混淆getTime()方法和getDay()及 getDate()方法，getDay()和getDate()返回的分别是一周的第几天和一个月的第几天。 
</em>

## Math（数学对象）

+ Math 算术函数和常量 
+ Math.abs( ) 计算绝对值 
+ Math.acos( ) 计算反余弦值 
+ Math.asin( ) 计算反正弦值 
+ Math.atan( ) 计算反正切值 
+ Math.atan2( ) 计算从x轴到一个点之间的角度 
+ Math.ceil( ) 对一个数上舍入 
+ Math.cos( ) 计算余弦值 
+ Math.E 算术常量e 
+ Math.exp( ) 计算ex 
+ Math.floor( ) 对一个数下舍入 
+ Math.LN10 算术常loge10 
+ Math.LN2 算术常量loge2 
+ Math.log( ) 计算一个数的自然对数 
+ Math.LOG10E 算术常量log10e 
+ Math.LOG2E 算术常量log2e 
+ Math.max( ) 返回最大的参数 
+ Math.min( ) 返回最小的参数 
+ Math.PI 算术常量PI 
+ Math.pow( ) 计算xy 
+ Math.random( ) 返回一个伪随机数 
+ Math.round( ) 舍入到最接近的整数 
+ Math.sin( ) 计算正弦值 
+ Math.sqrt( ) 计算平方根 
+ Math.SQRT1_2 算术常量 1/figs/squroot.gif 
+ Math.SQRT2 算术常量figs/squroot.gif 
+ Math.tan( ) 计算正切值 


### Math.abs(x)
返回x的绝对值.

* `Math.abs(x);`

### Math.pow(x,y)
返回x的y次幂.

* `Math.pow(base, exponent) `基数（base）、指数（exponent）)

* `ES7 写法 x**y`

### Math.random()
返回0到1之间的伪随机数.( 伪随机数在范围[0，1))

* `Math.random()`


**得到一个两数之间的随机数**

```
function getRandomArbitrary(min, max) {
  return Math.random() * (max - min) + min;
}

例如5到10间的随机数:Math.random()*5+5;
```

**得到一个两数之间的随机整数**

```js
function getRandomInt(min, max) {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min)) + min; //The maximum is exclusive and the minimum is inclusive
}
```

**得到一个两数之间的随机整数，包括两个数在内**

```js
function getRandomIntInclusive(min, max) {
  min = Math.ceil(min);
  max = Math.floor(max);
  return Math.floor(Math.random() * (max - min + 1)) + min; //The maximum is inclusive and the minimum is inclusive 
}
```


### Math.round(x)
返回四舍五入后的整数.

* `Math.round(x)`



### Math.ceil()
函数返回大于或等于一个给定数字的最小整数。

* `Math.ceil(x) `

```
Math.ceil(.95);    // 1
Math.ceil(4);      // 4
Math.ceil(7.004);  // 8
Math.ceil(-0.95);  // -0
Math.ceil(-4);     // -4
Math.ceil(-7.004); // -7
```



### Math.floor()
返回小于或等于一个给定数字的最大整数。

* `Math.floor(x) `

```
Math.floor( 45.95); 
// 45 
Math.floor( 45.05); 
// 45 
Math.floor( 4 ); 
// 4 
Math.floor(-45.05); 
// -46 
Math.floor(-45.95); 
// -46
```

### Round和Floor和Ceil

```js
// Closure
(function(){

	/**
	 * Decimal adjustment of a number.
	 *
	 * @param	{String}	type	The type of adjustment.
	 * @param	{Number}	value	The number.
	 * @param	{Integer}	exp		The exponent (the 10 logarithm of the adjustment base).
	 * @returns	{Number}			The adjusted value.
	 */
	function decimalAdjust(type, value, exp) {
		// If the exp is undefined or zero...
		if (typeof exp === 'undefined' || +exp === 0) {
			return Math[type](value);
		}
		value = +value;
		exp = +exp;
		// If the value is not a number or the exp is not an integer...
		if (isNaN(value) || !(typeof exp === 'number' && exp % 1 === 0)) {
			return NaN;
		}
		// Shift
		value = value.toString().split('e');
		value = Math[type](+(value[0] + 'e' + (value[1] ? (+value[1] - exp) : -exp)));
		// Shift back
		value = value.toString().split('e');
		return +(value[0] + 'e' + (value[1] ? (+value[1] + exp) : exp));
	}

	// Decimal round
	if (!Math.round10) {
		Math.round10 = function(value, exp) {
			return decimalAdjust('round', value, exp);
		};
	}
	// Decimal floor
	if (!Math.floor10) {
		Math.floor10 = function(value, exp) {
			return decimalAdjust('floor', value, exp);
		};
	}
	// Decimal ceil
	if (!Math.ceil10) {
		Math.ceil10 = function(value, exp) {
			return decimalAdjust('ceil', value, exp);
		};
	}

})();

// Round
Math.round10(55.55, -1); // 55.6
Math.round10(55.549, -1); // 55.5
Math.round10(55, 1); // 60
Math.round10(54.9, 1); // 50
Math.round10(-55.55, -1); // -55.5
Math.round10(-55.551, -1); // -55.6
Math.round10(-55, 1); // -50
Math.round10(-55.1, 1); // -60
// Floor
Math.floor10(55.59, -1); // 55.5
Math.floor10(59, 1); // 50
Math.floor10(-55.51, -1); // -55.6
Math.floor10(-51, 1); // -60
// Ceil
Math.ceil10(55.51, -1); // 55.6
Math.ceil10(51, 1); // 60
Math.ceil10(-55.59, -1); // -55.5
Math.ceil10(-59, 1); // -50

```

## BOM 浏览器对象模型
Browser-Object-Model

BOM (浏览器对象模型），它提供了与浏览器窗口进行交互的对象。

**整个浏览器窗口--window对象**

**地址栏--Location对象**

属性：

hash(哈希值)(锚点):""

host(主机):"127.0.0.1:8020"

hostname(主机名):"127.0.0.1"

href(当前网址):"http://127.0.0.1:8020/lanjing/blueji/javascript-9.3/mouse-9.11.html?__hbt=1536718057482"

origin(来源):"http://127.0.0.1:8020"

pathname(为文件路径):"/lanjing/blueji/javascript-9.3/mouse-9.11.html"

port(端口号):"8020"

protocol(协议):"http"


**历史纪录--History对象**

方法：

history.back();返回

history.forward();前进

history.go();可进可退

history.go(-1)==history.back();

history.go(1)==history.forward();

**浏览器信息--navigator对象**

获取浏览器内部代号，名称，操作系统等信息

获取用户什么设备打开网页的

navigator.UserAgent

## DOM 文档对象模型
DOM (document object model) 文档对象模型，它定义了操作文档对象的接口

### 一、获取元素节点对象

#### **1.querySelector()**

HTML5新加入的方法，通过传入合法的CSS选择器，即可获取符合条件的第一个元素

例：
```js
document.querySelector("#test"); //返回id为test的首个div
```

#### **2.querySelectorAll()**

HTML5新加入的方法，通过传入合法的CSS选择器，即可获取所有符合条件的元素，返回对象数组

> 主意：获取的是一个伪对象数组，无法直接使用数组特有的方法

`Object.prototype.toString.call(lili)`  `"[object NodeList]"`

例：
```js
document.querySelectorAll('div.foo')；//返回所有带foo类样式的div元素对象
```

> 要注意：使用这上面两个方法无法查找带伪类状态的元素，比如`querySelector(':hover')`不会得到期结果。

#### **3.getElementById()**

这个方法返回一个与给定id属性值的元素节点相对应的对象。

例：
```js
document.getElementById('box');
```

#### **4.getElementsByTagName()**

这个方法返回一个对象数组。每个对象分别对应着文档里给定标签的一个元素。

例：
```js
document.getElementsByTagName('li');
```

#### **5.getElementsByName() **
通过 name 获取一个对象数组


####  **6. el.getBoundingClienRect() **
返回矩形对象，left、top、right、bottom，分别代表元素距页面个边的距离


### 二、操作属性节点

#### **1.设置属性：setAttribute(属性名，属性值)**

```js
var div  = document.querySelector("#test"); 		//返回id为test的首个div
		div. setAttribute("class","testDiv");		//设置class属性为testDiv,<span>会覆盖掉class的原本属性</span>
```

#### **2. 获取属性：getAttribute(属性名)**

例：
```js
var div  = document.querySelector("#test"); 		//返回id为test的首个div
		div.getAttribute("class");		//获取class属性的值
```

#### **3.针对繁杂的CSS样式属性，我们可以使用:dom.style来进行控制**
**element.style只能读取行内样式**

例：  
```js
var div  = document.querySelector(“#test”); 		//返回id为test的首个div 	
		div.style.opacity = “.5”;		//设置透明度
		div.style.width = "100px";		//显示设置
		div.style.cssText =“background:red;border:1px solid blue”;    
		//一次性增加多个样式,*会覆盖上面两条属性设置*
```
> 推荐使用div.style.cssText +=“background:red;border:1px solid blue”; 

### 四、操作文本节点

#### **1.获取文本节点（包括子元素）：dom.innerHTML**

例：
```js
var div  = document.querySelector(“#test”); //返回id为test的首个div
alert(div. innerHTML);//获取div里的结构和内容
```

#### **2.获取文本节点（单纯文本）：dom.textContent**

例：
```js
var div  = document.querySelector(“#test”); //返回id为test的首个div
alert(div. textContent);//获取div里的纯文本内容
```

#### **3.设置文本节点（包括子元素）：dom.innerHTML**

例：
```js
var div  = document.querySelector(“#test”); //返回id为test的首个div
div. innerHTML= “<span>子元素span</span>”;//给div添加了一个子元素span
```

#### **4.设置文本节点（单纯文本）：dom.textContent**

例：
```js
var div  = document.querySelector(“#test”); //返回id为test的首个div
div. textContent= “我是一个div”;//设置div里的内容
```

### DOM节点的增删查改

```html
<ul>
  <li>
		1
		<span id=""></span>
  </li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
</ul>
```

```js
			var lis=document.getElementsByTagName("li");
			console.log(lis);  //HTMLCollection(5) [li, li, li, li, li]
			console.log(Object.prototype.toString.call(lis));  //[object HTMLCollection]
			
//			访问子节点
			console.log(lis[0].childNodes); //NodeList(3) [text, span, text] 有空白节点
			
```

```js
			//判断节点类型 nodeType(有数字组成)
			// 	节点类型
			// 1	元素节点Element  lis[0]
			// 2	属性节点Attr		lis[0].attributes.id.nodeType  如:id name  
			// 3	文本节点Text		lis[0].childNodes[0].nodeType
			// 8	注释节点Comment
			// 9	DOM树的根节点Document
```

#### 节点间互相访问(依赖于当前DOM)

* **(同级)访问下一个节点nextSibling**

`lis[1].nextSibling  //包括换行符，nextSibling:text`

* **(同级)访问上一个节点previousSibling**

`lis[1].previousSibling`

* **(同级)访问下一个元素节点nextElementSibling**

`lis[1].nextElementSibling //跟着元素节点时才有值，否则为null`

* **(同级)访问上一个元素节点previousElementSibling**

`lis[1].previousElementSibling`

* **访问上级(父级)节点parentNode**

`lis[1].parentNode`

* **访问上级(父级)元素节点parentElement**

`lis[1].parentElement`

#### DOM节点的增删查改
```html
<!--html-->
<ul id="ul">
	<li>我是本来第一个</li>
	<li>我是本来第二个</li>
	<li>我是本来第三个</li>
</ul>
```

* **appendChild(新元素);**
```js
		var ul=document.querySelector("#ul");
		var li=document.createElement("li");
		li.className="red fl";
		li.innerHTML="我是新来li";
		li1.innerHTML="我是也新来li1";
		li.style.color="skyblue";
		ul.appendChild(li);
```
* **insertBefore(新元素,目标元素)**
```js
		ul.insertBefore(li,ul.children[0])
```

* **removeChild(目标元素)**
```js
		ul.removeChild(ul.children[2]);
```

* **cloneNode(true);**
如果不克隆容器里的属性和方法，不需深拷贝，则去掉true
```js
		var cli=ul.children[0].cloneNode(true);
		cli.innerHTML="我是克隆的";
		ul.insertBefore(cli,ul.children[0])
```

### DOM元素对象

**定义和用法**

classList 属性返回元素的类名，作为 DOMTokenList 对象。

该属性用于在元素中添加，移除及切换 CSS 类。

classList 属性是只读的，但你可以使用 add() 和 remove() 方法修改它。

**语法**

* `element.classList`

**方法**

* **add(class1, class2, ...)**	

在元素中添加一个或多个类名。如果指定的类名已存在，则不会添加

* **contains(class)**	

返回布尔值，判断指定的类名是否存在。
可能值：

true - 元素包已经包含了该类名

false - 元素中不存在该类名

* **item(index)**	


返回类名在元素中的索引值。索引值从 0 开始。

如果索引值在区间范围外则返回 null

* **remove(class1, class2, ...)**	

移除元素中一个或多个类名。

> 注意： 移除不存在的类名，不会报错。


* **toggle(class, true|false)	**

在元素中切换类名。

第一个参数为要在元素中移除的类名，并返回 false。 
如果该类名不存在则会在元素中添加类名，并返回 true。 

第二个是可选参数，是个布尔值用于设置元素是否强制添加或移除类，不管该类名是否存在。

例如：
```
移除一个 class: element.classList.toggle("classToRemove", false); 
添加一个 class: element.classList.toggle("classToAdd", true);
```

> 注意： Internet Explorer 或 Opera 12 及其更早版本不支持第二个参数。

```html
			.baba{
				width: 200px;
				height: 200px;
				background-color: skyblue;
			}
			.baba2{
				border-radius: 50%;
			}
			.bb{
				font-size: 50px;
			}
			.cc{
				margin: 0 auto;
			}
		<div class="baba aa baba2" id="container">
			baba
		</div>

			var container=document.querySelector("#container");
			console.log(container.classList);
			container.addEventListener("click",function(){
				container.classList.add("cc","bb");
				container.classList.remove("baba2");
				container.classList.toggle("baba2");
				container.classList.contains("baba2")?container.classList.remove("baba2"):container.classList.add("baba2");
			})
```

## 闭包
闭包就是能够读取其他函数内部变量的函数。    
例如在javascript中，只有函数内部的子函数才能读取局部变量，所以闭包可以理解成“定义在一个函数内部的函数“。    
在本质上，闭包是将函数内部和函数外部连接起来的桥梁.    


**上代码:**
```js
function a(){
	var i=0;
	function b(){
		alert(++i);
	}
	return b;
}
var c=a();
c();
```

这段代码有两个特点：    
1、函数b嵌套在函数a内部；    
2、函数a返回函数b。    
这样在执行完var c=a( )后，变量c实际上是指向了函数b，再执行c( )后就会弹出一个窗口显示i的值（第一次为1）。    
这段代码其实就创建了一个闭包，这是因为函数a外的变量c引用了函数a内的函数b。    
也就是说，当函数a的内部函数b被函数a外的一个变量引用的时候，就创建了一个闭包。

> 简而言之，
闭包的作用就是在a执行完并返回后，闭包使得Javascript的垃圾回收机制不会收回a所占用的资源，因为a的内部函数b的执行需要依赖a中的变量。    
 