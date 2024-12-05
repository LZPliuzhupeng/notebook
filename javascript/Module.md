
# Module

<!-- [廖雪峰 ECMAScript 6 入门--Module 的语法](https://es6.ruanyifeng.com/#docs/module) -->

在 ES6 之前，社区制定了一些模块加载方案，最主要的有 CommonJS 和 AMD 两种。前者用于服务器，后者用于浏览器。ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。  

CommonJS 模块
```js
// CommonJS模块
let { stat, exists, readfile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```
上面代码的实质是整体加载fs模块（即加载fs的所有方法），生成一个对象（_fs），然后再从这个对象上面读取 3 个方法。这种加载称为`“运行时加载”`，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

ES6 模块是从fs模块加载 3 个方法，其他方法不加载。这种加载称为`“编译时加载”`或者`静态加载`，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。
```js
// ES6模块
import { stat, exists, readFile } from 'fs';
```

## export 

**模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。**
```js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```

```js
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export { firstName, lastName, year };
```
> 两种写法是等价的，推荐第二种写法。

**as**

**通常情况下，export输出的变量就是本来的名字，但是可以使用as关键字重命名。**
```js
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2
};
```

**需要特别注意的是，export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。**
```js
// 报错
export 1;

// 报错
var m = 1;
export m;
```
> 上面两种写法都会报错，因为没有提供对外的接口。第一种写法直接输出 1，第二种写法通过变量m，还是直接输出 1。1只是一个值，不是接口。

**正确的写法**
```js
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```
> 上面三种写法都是正确的，规定了对外的接口m。其他脚本可以通过这个接口，取到值1。它们的实质是，在接口名与模块内部变量之间，建立了一一对应的关系。

**export命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错.**
```js
function foo() {
  export default 'bar' // SyntaxError
}
foo()
```

## import

使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过import命令加载这个模块。
```js
// main.js
import { firstName, lastName, year } from './profile.js';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

**as**

```js
import { lastName as surname } from './profile.js';
```

**import命令输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口。**
```js
import {a} from './xxx.js'

a = {}; // Syntax Error : 'a' is read-only;
```
> 脚本加载了变量a，对其重新赋值就会报错，因为a是一个只读的接口

**如果a是一个对象，改写a的属性是允许的。**
```js
import {a} from './xxx.js'

a.foo = 'hello'; // 合法操作
```
> a的属性可以成功改写，并且其他模块也可以读到改写后的值。

!> 不过，这种写法很难查错，建议凡是输入的变量，都当作完全只读，不要轻易改变它的属性。


**`import`后面的from指定模块文件的位置，可以是相对路径，也可以是绝对路径。如果不带有路径，只是一个模块名，那么必须有配置文件，告诉 JavaScript 引擎该模块的位置。**
```js
import { myMethod } from 'util';
```

**import命令具有提升效果，会提升到整个模块的头部，首先执行。**
```js
foo();
import { foo } from 'my_module';
```
> import的执行早于foo的调用。这种行为的本质是，import命令是编译阶段执行的，在代码运行之前。


**由于import是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。**
```js
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```
> 上面三种写法都会报错，因为它们用到了表达式、变量和if结构。在静态分析阶段，这些语法都是没法得到值的。

**import语句会执行所加载的模块**
```js
import 'lodash';
```

**多次重复执行同一句import语句，那么只会执行一次，而不会执行多次**
```js
import 'lodash';
import 'lodash';
```
```js
import { foo } from 'my_module';
import { bar } from 'my_module';

// 等同于
import { foo, bar } from 'my_module';
```

## 模块的整体加载

除了指定加载某个输出值，还可以使用整体加载，即用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面。

**下面是一个`circle.js`文件，它输出两个方法`area`和`circumference`。**
```js
// circle.js
export function area(radius) {
  return Math.PI * radius * radius;
}

export function circumference(radius) {
  return 2 * Math.PI * radius;
}
```

```js
// main.js

import { area, circumference } from './circle';

console.log('圆面积：' + area(4));
console.log('圆周长：' + circumference(14));
```

```js
import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```

**注意，模块整体加载所在的那个对象（上例是`circle`），应该是可以静态分析的，所以不允许运行时改变。下面的写法都是不允许的。**
```js
import * as circle from './circle';

// 下面两行都是不允许的
circle.foo = 'hello';
circle.area = function () {};
```

## export default

使用`import`命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。

**`export default`命令，为模块指定默认输出。其他模块加载该模块时，`import`命令可以为该匿名函数指定任意名字。**  
```js
// export-default.js
export default function () {
  console.log('foo');
}
```
```js
// import-default.js
import customName from './export-default';
customName(); // 'foo'
```

**foo函数的函数名foo，在模块外部是无效的。加载的时候，视同匿名函数加载。**
```js
// export-default.js
export default function foo() {
  console.log('foo');
}

// 或者写成

function foo() {
  console.log('foo');
}

export default foo;
```

**默认输出和正常输出**
```js
// 第一组
export default function crc32() { // 输出
  // ...
}
import crc32 from 'crc32'; // 输入

// 第二组
export function crc32() { // 输出
  // ...
};
import {crc32} from 'crc32'; // 输入
```
> 第一组是使用`export default`时，对应的`import`语句不需要使用大括号；第二组是不使用`export default`时，对应的`import`语句需要使用大括号。

`export default`命令用于指定模块的默认输出。显然，一个模块只能有一个默认输出，因此`export default`命令只能使用一次。所以，`import`命令后面才不用加大括号，因为只可能唯一对应`export default`命令。

**本质上，`export default`就是输出一个叫做`default`的变量或方法，然后系统允许你为它取任意名字。**
```js
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as foo } from 'modules';
// 等同于
// import foo from 'modules';
```

**因为`export default`命令其实只是输出一个叫做`default`的变量，所以它后面不能跟变量声明语句**
```js
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 错误
export default var a = 1;
```
> `export default a`的含义是将变量a的值赋给变量`default`。所以，最后一种写法会报错。

**有了`export default`命令，输入模块时就非常直观了，以输入 lodash 模块为例。**

暴露出`forEach`接口，默认指向`each`接口，即`forEach`和`each`指向同一个方法。
```js
export default function (obj) {
  // ···
}

export function each(obj, iterator, context) {
  // ···
}

export { each as forEach };
```
```js
import _ from 'lodash';
```

**同时输入默认方法和其他接口**
```js
import _, { each, forEach } from 'lodash';
```

**`export default`也可以用来输出类。**
```js
// MyClass.js
export default class { ... }

// main.js
import MyClass from 'MyClass';
let o = new MyClass();
```

## export 与 import 的复合写法

**如果在一个模块之中，先输入后输出同一个模块，import语句可以与export语句写在一起。**
```js
export { foo, bar } from 'my_module';

// 可以简单理解为
import { foo, bar } from 'my_module';
export { foo, bar };
```
!> `export`和`import`语句可以结合在一起写成一行以后，`foo`和`bar`实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用`foo`和`bar`。

**模块的接口改名和整体输出**

**默认接口的写法**
```js
export { default } from 'foo';
```

**具名接口改为默认接口的写法**
```js
export { es6 as default } from './someModule';

// 等同于
import { es6 } from './someModule';
export default es6;
```

**默认接口也可以改名为具名接口**
```js
export { default as es6 } from './someModule';
```

ES2020 之前，有一种import语句，没有对应的复合写法。
```js
import * as someIdentifier from "someModule";
```
ES2020补上了这个写法。
```js
export * as ns from "mod";

// 等同于
import * as ns from "mod";
export {ns};
```

## 模块的继承

略。。。

## 跨模块常量

略。。。

## import()

`import`命令会被 JavaScript 引擎静态分析，先于模块内的其他语句执行（`import`命令叫做“连接” binding 其实更合适）。

报错
```js
// 报错
if (x === 2) {
  import MyModual from './myModual';
}
```
> 引擎处理`import`语句是在编译时，这时不会去分析或执行if语句，所以`import`语句放在if代码块之中毫无意义，因此会报句法错误，而不是执行时错误。也就是说，`import`和`export`命令只能在模块的顶层，不能在代码块之中（比如，在if代码块之中，或在函数之中）。

这样的设计，固然有利于编译器提高效率，但也导致无法在运行时加载模块。在语法上，条件加载就不可能实现。如果`import`命令要取代 Node 的`require`方法，这就形成了一个障碍。因为`require`是运行时加载模块，`import`命令无法取代`require`的动态加载功能。
```js
const path = './' + fileName;
const myModual = require(path);
```
> require到底加载哪一个模块，只有运行时才知道。import命令做不到这一点。

**ES2020提案 引入`import()`函数，支持动态加载模块。**

**import()返回一个 Promise 对象。**
```js
const main = document.querySelector('main');

import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main);
  })
  .catch(err => {
    main.textContent = err.message;
  });
```
> `import()`函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。它是运行时执行，也就是说，什么时候运行到这一句，就会加载指定的模块。另外，`import()`函数与所加载的模块没有静态连接关系，这点也是与`import`语句不相同。`import()`类似于 Node 的`require`方法，区别主要是前者是异步加载，后者是同步加载。

**import()加载模块成功以后，这个模块会作为一个对象，当作then方法的参数。**
```js
import('./myModule.js')
.then(({export1, export2}) => {
  // ...·
});
```

**如果模块有default输出接口，可以用参数直接获得。**
```js
import('./myModule.js')
.then(myModule => {
  console.log(myModule.default);
});

import('./myModule.js')
.then(({default: theDefault}) => {
  console.log(theDefault);
});
```

**如果想同时加载多个模块**
```js
Promise.all([
  import('./module1.js'),
  import('./module2.js'),
  import('./module3.js'),
])
.then(([module1, module2, module3]) => {
   ···
});
```

**import()也可以用在 async 函数之中。**
```js
async function main() {
  const myModule = await import('./myModule.js');
  const {export1, export2} = await import('./myModule.js');
  const [module1, module2, module3] =
    await Promise.all([
      import('./module1.js'),
      import('./module2.js'),
      import('./module3.js'),
    ]);
}
main();
```

## 向后兼容

ECMAScript 6模块是作为一整块JavaScript代码而存在的。带有`type="module"`属性的`<script>`标签会告诉浏览器相关代码应该作为模块执行，而不是作为传统的脚本执行。模块可以嵌入在网页中，也可以作为外部文件引入：
```js
<script type="module">
  // 模块代码
</script>

<script type="module" src="path/to/myModule.js"></script>
```

浏览器在遇到`<script>`标签上无法识别的type属性时会拒绝执行其内容。对于不支持模块的浏览器，这意味着`<script type="module">`不会被执行。因此，可以在`<script type="module">`标签旁边添加一个回退`<script>`标签：
```js
// 不支持模块的浏览器不会执行这里的代码
<script type="module" src="module.js"></script>

// 不支持模块的浏览器会执行这里的代码
<script src="script.js"></script>
```
**这样一来支持模块的浏览器就有麻烦了。此时，前面的代码会执行两次。**

为了避免这种情况，原生支持ECMAScript 6模块的浏览器也会识别`nomodule`属性。此属性通知支持ES6模块的浏览器不执行脚本。不支持模块的浏览器无法识别该属性，从而忽略这个属性的存在。
```js
// 支持模块的浏览器会执行这段脚本
// 不支持模块的浏览器不会执行这段脚本
<script type="module" src="module.js"></script>

// 支持模块的浏览器不会执行这段脚本
// 不支持模块的浏览器会执行这段脚本
<script nomodule src="script.js"></script>
```

## AMD、CMD、CommonJS、ES6 Module

### AMD
AMD一开始是CommonJS规范中的一个草案，全称是Asynchronous Module Definition，即异步模块加载机制。后来由该草案的作者以RequireJS实现了AMD规范，所以一般说AMD也是指RequireJS。

**RequireJS**

通过define来定义一个模块，使用require可以导入定义的模块。
```js
//a.js
//define可以传入三个参数，分别是字符串-模块名、数组-依赖模块、函数-回调函数
define(function(){
    return 1;
})

// b.js
//数组中声明需要加载的模块，可以是模块名、js文件路径
require(['a'], function(a){
　　console.log(a);// 1
});
```
> 特点：对于依赖的模块，AMD推崇**依赖前置，提前执行**。也就是说，在`define`方法里传入的依赖模块(数组)，会在一开始就下载并执行

### CMD

CMD是SeaJS在推广过程中生产的对模块定义的规范，在Web浏览器端的模块加载器中，SeaJS与RequireJS并称，SeaJS作者为阿里的玉伯。
```js
//a.js
/*
* define 接受 factory 参数，factory 可以是一个函数，也可以是一个对象或字符串,
* factory 为对象、字符串时，表示模块的接口就是该对象、字符串。
* define 也可以接受两个以上参数。字符串 id 表示模块标识，数组 deps 是模块依赖.
*/
define(function(require, exports, module) {
  var $ = require('jquery');

  exports.setColor = function() {
    $('body').css('color','#333');
  };
});

//b.js
//数组中声明需要加载的模块，可以是模块名、js文件路径
seajs.use(['a'], function(a) {
  $('#el').click(a.setColor);
});
```
> 特点：对于依赖的模块，CMD推崇**依赖就近，延迟执行**。也就是说，只有到`require`时依赖模块才执行。

### CommonJS

CommonJS规范为CommonJS小组所提出，目的是弥补JavaScript在服务器端缺少模块化机制，NodeJS、webpack都是基于该规范来实现的。
```js
//a.js
module.exports = function () {
  console.log("hello world")
}

//b.js
var a = require('./a');

a();//"hello world"

//或者

//a2.js
exports.num = 1;
exports.obj = {xx: 2};

//b2.js
var a2 = require('./a2');

console.log(a2);//{ num: 1, obj: { xx: 2 } }
```

**CommonJS的特点**
+ 所有代码都运行在模块作用域，不会污染全局作用域；

+ 模块是同步加载的，即只有加载完成，才能执行后面的操作；

+ 模块在首次执行后就会缓存，再次加载只返回缓存结果，如果想要再次执行，可清除缓存；

+ CommonJS输出是值的拷贝(即，require返回的值是被输出的值的拷贝，模块内部的变化也不会影响这个值)。

### ES6 Module

```js
//a.js
var name = 'lin';
var age = 13;
var job = 'ninja';

export { name, age, job};

//b.js
import { name, age, job} from './a.js';

console.log(name, age, job);// lin 13 ninja

//或者

//a2.js
export default function () {
  console.log('default ');
}

//b2.js
import customName from './a2.js';
customName(); // 'default'
```

**ES6 Module的特点(对比CommonJS)**
+ CommonJS模块是运行时加载，ES6 Module是编译时输出接口；

+ CommonJS加载的是整个模块，将所有的接口全部加载进来，ES6 Module可以单独加载其中的某个接口；

+ CommonJS输出是值的拷贝，ES6 Module输出的是值的引用，被输出模块的内部的改变会影响引用的改变；

+ CommonJS this指向当前模块，ES6 Module this指向undefined;