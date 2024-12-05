# 对象、类与面向对象编程

+ ECMA-262将对象定义为一组属性的`无序集合`

+ 对象的每个属性或方法都由一个名称来标识，这个名称映射到一个值

+ 正因为如此（以及其他还未讨论的原因），可以把ECMAScript的对象想象成一张`散列表`，其中的内容就是一组`名/值对`，值可以是数据或者函数。

## 对象

### 理解对象

创建自定义对象的通常方式是创建Object的一个新实例，然后再给它添加属性和方法，如下例所示：

```js
let person = new Object();
person.name = "Nicholas";
person.age = 29;
person.job = "Software Engineer";
person.sayName = function() {
  console.log(this.name);
};
```

对象字面量写法：

```js
let person = {
  name: "Nicholas",
  age: 29,
  job: "Software Engineer",
  sayName() {
    console.log(this.name);
  }
};
```

这个例子中的person对象跟前面例子中的person对象是等价的，它们的属性和方法都一样。这些属性都有自己的特征，而这些特征决定了它们在JavaScript中的行为。

### 属性的类型

ECMA-262使用一些内部特性来描述属性的特征。这些特性是由为JavaScript实现引擎的规范定义的。

因此，开发者不能在JavaScript中直接访问这些特性。为了将某个特性标识为内部特性，规范会用两个中括号把特性的名称括起来，比如`[[Enumerable]]`。

?> **属性分两种：数据属性和访问器属性。**

#### 数据属性

+ `[[Configurable]]`：表示属性是否可以通过`delete`删除并重新定义，是否可以修改它的特性，以及是否可以把它改为访问器属性。默认情况下，所有直接定义在对象上的属性的这个特性都是`true`，如前面的例子所示。

+ `[[Enumerable]]`：表示属性是否可以通过for-in循环返回。默认情况下，所有直接定义在对象上的属性的这个特性都是true，如前面的例子所示。

+ `[[Writable]]`：表示属性的值是否可以被修改。默认情况下，所有直接定义在对象上的属性的这个特性都是true，如前面的例子所示。

+ `[[Value]]`：包含属性实际的值。这就是前面提到的那个读取和写入属性值的位置。这个特性的默认值为undefined。


在像前面例子中那样将属性显式添加到对象之后，`[[Configurable]]`、`[[Enumerable]]`和`[[Writable]]`都会被设置为true，而`[[Value]]`特性会被设置为指定的值

```js
let person = {
  name: "Nicholas"
};
```

?> **要修改属性的默认特性，就必须使用`Object.defineProperty()`方法**

这个方法接收3个参数：`要给其添加属性的对象`、`属性的名称`和`一个描述符对象`。

最后一个参数，即描述符对象上的属性可以包含：`configurable`、`enumerable`、`writable`和`value`，跟相关特性的名称一一对应。根据要修改的特性，可以设置其中一个或多个值。比如：

```js
let person = {};
Object.defineProperty(person, "name", {
  writable: false,
  value: "Nicholas"
});
console.log(person.name); // "Nicholas"
person.name = "Greg";
console.log(person.name); // "Nicholas"
```
这个例子创建了一个名为name的属性并给它赋予了一个只读的值"Nicholas"。

> 这个属性的值就不能再修改了，在非严格模式下尝试给这个属性重新赋值会被忽略。在严格模式下，尝试修改只读属性的值会抛出错误。

类似的规则也适用于创建不可配置的属性`[[Configurable]]`。比如：

```js
let person = {};
Object.defineProperty(person, "name", {
  configurable: false,
  value: "Nicholas"
});
console.log(person.name); // "Nicholas"
delete person.name;
console.log(person.name); // "Nicholas"
```

这个例子把`configurable`设置为`false`，意味着这个属性不能从对象上删除。非严格模式下对这个属性调用delete没有效果，严格模式下会抛出错误。

此外，一个属性被定义为不可配置之后，就`不能再变回可配置`的了。再次调用`Object.defineProperty()`并修改任何非`writable`属性会导致错误：

```js
let person = {};
Object.defineProperty(person, "name", {
  configurable: false,
  value: "Nicholas"
});

// 抛出错误
Object.defineProperty(person, "name", {
  configurable: true,
  value: "Nicholas"
});
```

> 在调用`Object.defineProperty()`时，`configurable`、`enumerable`和`writable`的值如果不指定，则都默认为`false`。

#### 访问器属性

访问器属性不包含数据值。相反，它们包含一个获取（getter）函数和一个设置（setter）函数，不过这两个函数不是必需的。

+ `[[Get]]`：获取函数，在读取属性时调用。默认值为`undefined`。

+ `[[Set]]`：设置函数，在写入属性时调用。默认值为`undefined`。

访问器属性是不能直接定义的，必须使用`Object.defineProperty()`。下面是一个例子：

```js
// 定义一个对象，包含伪私有成员year_和公共成员edition
let book = {
  year_: 2017,
  edition: 1
};

Object.defineProperty(book, "year", {
  get() {
    return this.year_;
  },
  set(newValue) {
    if (newValue > 2017) {
      this.year_ = newValue;
      this.edition += newValue - 2017;
    }
  }
});
book.year = 2018;
console.log(book.edition); // 2
```

> `year_`中的`下划线`常用来表示该属性并不希望在对象方法的外部被访问

获取函数和设置函数不一定都要定义。

?> 只定义获取函数意味着属性是只读的，尝试修改属性会被忽略。

在严格模式下，尝试写入只定义了获取函数的属性会抛出错误。类似地，只有一个设置函数的属性是不能读取的，非严格模式下读取会返回`undefined`，严格模式下会抛出错误。

在不支持`Object.defineProperty()`的浏览器中没有办法修改`[[Configurable]]`或`[[Enumerable]]`。

!> 注意:在ECMAScript 5以前，开发者会使用两个非标准的访问创建访问器属性：`__defineGetter__()`和`__defineSetter__()`。这两个方法最早是Firefox引入的，后来Safari、Chrome和Opera也实现了。


#### 定义多个属性

ECMAScript提供了`Object.defineProperties()`通过多个描述符一次性定义多个属性.

它接收两个参数：`要为之添加或修改属性的对象`和`另一个描述符对象`，其属性与要添加或修改的属性一一对应。比如：

```js
let book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017
  },

  edition: {
    value: 1
  },

  year: {
    get() {
      return this.year_;
    },

    set(newValue) {
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    }
  }
});
```

这段代码在`book`对象上定义了两个数据属性`year_`和`edition`，还有一个访问器属性`year`。所有属性都是同时定义的，并且数据属性的`configurable`、`enumerable`和`writable`特性值都是`false`。

#### 读取属性的特性

使用`Object.getOwnPropertyDescriptor()`方法可以取得指定属性的属性描述符。

这个方法接收两个参数：`属性所在的对象`和`要取得其描述符的属性名`。

返回值是一个对象，对于访问器属性包含`configurable`、`enumerable`、`get`和`set`属性，对于数据属性包含`configurable`、`enumerable`、`writable`和`value`属性。比如：

```js
let book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017
  },

  edition: {
    value: 1
  },

  year: {
    get: function() {
      return this.year_;
    },

    set: function(newValue){
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    }
  }
});

let descriptor = Object.getOwnPropertyDescriptor(book, "year_");
console.log(descriptor.value);          // 2017
console.log(descriptor.configurable);   // false
console.log(typeof descriptor.get);     // "undefined"
let descriptor = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor.value);          // undefined
console.log(descriptor.enumerable);     // false
console.log(typeof descriptor.get);     // "function"
```

对于数据属性`year_`，`value`等于原来的值，`configurable`是`false`，`get`是`undefined`。对于访问器属性`year`，`value`是`undefined`，`enumerable`是`false`，`get`是一个指向获取函数的指针。


**ECMAScript 2017新增了`Object.getOwnPropertyDescriptors()`静态方法。这个方法实际上会在每个自有属性上调用`Object.getOwnPropertyDescriptor()`并在一个新对象中返回它们。**

```js
let book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017
  },

  edition: {
    value: 1
  },

  year: {
    get: function() {
      return this.year_;
    },

    set: function(newValue){
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    }
  }
});

console.log(Object.getOwnPropertyDescriptors(book));
// {
//   edition: {
//     configurable: false,
//     enumerable: false,
//     value: 1,
//     writable: false
//   },
//   year: {
//     configurable: false,
//     enumerable: false,
//     get: f(),
//     set: f(newValue),
//   },
//   year_: {
//     configurable: false,
//     enumerable: false,
//     value: 2017,
//     writable: false
//   }
// }
```

### 合并对象

ECMAScript 6专门为合并对象提供了`Object.assign()`方法。

这个方法接收一个目标对象和一个或多个源对象作为参数，然后将每个源对象中可枚举（`Object.propertyIsEnumerable()`返回`true`）和自有（`Object.hasOwnProperty()`返回`true`）属性复制到目标对象。

以字符串和符号为键的属性会被复制。对每个符合条件的属性，这个方法会使用源对象上的`[[Get]]`取得属性的值，然后使用目标对象上的`[[Set]]`设置属性的值。

```js
let dest, src, result;

/**
 * 简单复制
 */
dest = {};
src = { id: 'src' };

result = Object.assign(dest, src);

// Object.assign修改目标对象
// 也会返回修改后的目标对象
console.log(dest === result); // true
console.log(dest !== src);    // true
console.log(result);          // { id: src }
console.log(dest);            // { id: src }


/**
 * 多个源对象
 */
dest = {};

result = Object.assign(dest, { a: 'foo' }, { b: 'bar' });

console.log(result); // { a: foo, b: bar }


/**
 * 获取函数与设置函数
 */
dest = {
  set a(val) {
    console.log(`Invoked dest setter with param ${val}`);
  }
};
src = {
  get a() {
    console.log('Invoked src getter');
    return 'foo';
  }
};

Object.assign(dest, src);
// 调用src的获取方法
// 调用dest的设置方法并传入参数"foo"
// 因为这里的设置函数不执行赋值操作
// 所以实际上并没有把值转移过来
console.log(dest); // { set a(val) {...} }
```

**`Object.assign()`实际上对每个源对象执行的是浅复制。如果多个源对象都有相同的属性，则使用最后一个复制的值。此外，从源对象访问器属性取得的值，比如获取函数，会作为一个静态值赋给目标对象。换句话说，不能在两个对象间转移获取函数和设置函数。**

```js
let dest, src, result;

/**
 * 覆盖属性
 */
dest = { id: 'dest' };

result = Object.assign(dest, { id: 'src1', a: 'foo' }, { id: 'src2', b: 'bar' });

// Object.assign会覆盖重复的属性
console.log(result); // { id: src2, a: foo, b: bar }

// 可以通过目标对象上的设置函数观察到覆盖的过程：
dest = {
  set id(x) {
    console.log(x);
  }
};

Object.assign(dest, { id: 'first' }, { id: 'second' }, { id: 'third' });
// first
// second
// third


/**
 * 对象引用
 */

dest = {};
src = { a: {} };

Object.assign(dest, src);

// 浅复制意味着只会复制对象的引用
console.log(dest);              // { a :{} }
console.log(dest.a === src.a);  // true
```

**如果赋值期间出错，则操作会中止并退出，同时抛出错误。`Object.assign()`没有“回滚”之前赋值的概念，因此它是一个尽力而为、可能只会完成部分复制的方法。**

```js
let dest, src, result;

/**
 * 错误处理
 */
dest = {};
src = {
  a: 'foo',
  get b() {
    // Object.assign()在调用这个获取函数时会抛出错误
    throw new Error();
  },
  c: 'bar'
};

try {
  Object.assign(dest, src);
} catch(e) {}

// Object.assign()没办法回滚已经完成的修改
// 因此在抛出错误之前，目标对象上已经完成的修改会继续存在：
console.log(dest); // { a: foo }
```

### 对象标识及相等判定

**在ECMAScript 6之前，有些特殊情况即使是===操作符也无能为力：**

```js
// 这些是===符合预期的情况
console.log(true === 1);  // false
console.log({} === {});   // false
console.log("2" === 2);   // false

// 这些情况在不同JavaScript引擎中表现不同，但仍被认为相等
console.log(+0 === -0);   // true
console.log(+0 === 0);    // true
console.log(-0 === 0);    // true

// 要确定NaN的相等性，必须使用极为讨厌的isNaN()
console.log(NaN === NaN); // false
console.log(isNaN(NaN));  // true
```

**为改善这类情况，ECMAScript 6规范新增了`Object.is()`，这个方法与===很像，但同时也考虑到了上述边界情形。这个方法必须接收两个参数：**

```js
console.log(Object.is(true, 1));  // false
console.log(Object.is({}, {}));   // false
console.log(Object.is("2", 2));   // false

// 正确的0、-0、+0相等/不等判定
console.log(Object.is(+0, -0));   // false
console.log(Object.is(+0, 0));    // true
console.log(Object.is(-0, 0));    // false

// 正确的NaN相等判定
console.log(Object.is(NaN, NaN)); // true
```

**要检查超过两个值，递归地利用相等性传递即可：**

```js
function recursivelyCheckEqual(x, ...rest) {
  return Object.is(x, rest[0]) &&
         (rest.length < 2 || recursivelyCheckEqual(...rest));
}
```

### 增强的对象语法

ECMAScript 6为定义和操作对象新增了很多极其有用的语法糖特性。这些特性都没有改变现有引擎的行为，但极大地提升了处理对象的方便程度。

```js
let name = 'Matt';

let person = {
  name
};
```

**可计算属性**

有了可计算属性，就可以在对象字面量中完成动态属性赋值。中括号包围的对象属性键告诉运行时将其作为JavaScript表达式而不是字符串来求值：

```js
const nameKey = 'name';
const ageKey = 'age';
const jobKey = 'job';

let person = {
  [nameKey]: 'Matt',
  [ageKey]: 27,
  [jobKey]: 'Software engineer'
};

console.log(person); // { name: 'Matt', age: 27, job: 'Software engineer' }
```
```js
const nameKey = 'name';
const ageKey = 'age';
const jobKey = 'job';
let uniqueToken = 0;

function getUniqueKey(key) {
  return `${key}_${uniqueToken++}`;
}

let person = {
  [getUniqueKey(nameKey)]: 'Matt',
  [getUniqueKey(ageKey)]: 27,
  [getUniqueKey(jobKey)]: 'Software engineer'
};

console.log(person);  // { name_0: 'Matt', age_1: 27, job_2: 'Software engineer' }
```
!> 注意　可计算属性表达式中抛出任何错误都会中断对象创建。如果计算属性的表达式有副作用，那就要小心了，因为如果表达式抛出错误，那么之前完成的计算是不能回滚的。

**简写方法名**

```js
// let person = {
//   sayName: function(name) {
//     console.log(`My name is ${name}`);
//   }
// }; 

let person = {
  sayName(name) {
    console.log(`My name is ${name}`);
  }
};

person.sayName('Matt'); // My name is Matt
```

```js
const methodKey = 'sayName';

let person = {
  [methodKey](name) {
    console.log(`My name is ${name}`);
  }
}

person.sayName('Matt'); // My name is Matt
```
!> 注意　简写方法名对于本章后面介绍的ECMAScript 6的类更有用。

### 对象解构

```js
// // 不使用对象解构
// let person = {
//   name: 'Matt',
//   age: 27
// };

// let personName = person.name,
//     personAge = person.age;

// console.log(personName); // Matt
// console.log(personAge);  // 27

let person = {
  name: 'Matt',
  age: 27
};

let { name, age, job, jobDefault='Software engineer' } = person;

console.log(name);  // Matt
console.log(age);   // 27
console.log(job);   // undefined
console.log(jobDefault);   // Software engineer
```

**解构在内部使用函数`ToObject()`（不能在运行时环境中直接访问）把源数据结构转换为对象。这意味着在对象解构的上下文中，原始值会被当成对象。这也意味着（根据`ToObject()`的定义），`null`和`undefined`不能被解构，否则会抛出错误。**

```js
let { length } = 'foobar';
console.log(length);        // 6

let { constructor: c } = 4;
console.log(c === Number);  // true

let { _ } = null;           // TypeError

let { _ } = undefined;      // TypeError
```

**解构并不要求变量必须在解构表达式中声明。不过，如果是给事先声明的变量赋值，则赋值表达式必须包含在一对括号中：**

> 新发现

```js
let personName, personAge;

let person = {
  name: 'Matt',
  age: 27
};

({name: personName, age: personAge} = person);

console.log(personName, personAge); // Matt, 27
```

#### 嵌套解构

```js
let person = {
  name: 'Matt',
  age: 27,
  job: {
    title: 'Software engineer'
  }
};
let personCopy = {};
　
　
({
  name: personCopy.name,
  age: personCopy.age,
  job: personCopy.job
} = person);

// 因为一个对象的引用被赋值给personCopy，所以修改
// person.job对象的属性也会影响personCopy
person.job.title = 'Hacker'

console.log(person);
// { name: 'Matt', age: 27, job: { title: 'Hacker' } }

console.log(personCopy);
// { name: 'Matt', age: 27, job: { title: 'Hacker' } }
```

```js
let person = {
  name: 'Matt',
  age: 27,
  job: {
    title: 'Software engineer'
  }
};

// 声明title变量并将person.job.title的值赋给它
let { job: { title } } = person;

console.log(title); // Software engineer
```

**在外层属性没有定义的情况下不能使用嵌套解构。无论源对象还是目标对象都一样：**

```js
let person = {
  job: {
    title: 'Software engineer'
  }
};
let personCopy = {};

// foo在源对象上是undefined
({
  foo: {
    bar: personCopy.bar
  }
} = person);
// TypeError: Cannot destructure property 'bar' of 'undefined' or 'null'.

// job在目标对象上是undefined
({
  job: {
    title: personCopy.job.title
  }
} = person);
// TypeError: Cannot set property 'title' of undefined
```

#### 部分解构

需要注意的是，涉及多个属性的解构赋值是一个输出无关的顺序化操作。如果一个解构表达式涉及多个赋值，开始的赋值成功而后面的赋值出错，则整个解构赋值只会完成一部分：

```js
let person = {
  name: 'Matt',
  age: 27
};

let personName, personBar, personAge;

try {
  // person.foo是undefined，因此会抛出错误
  ({name: personName, foo: { bar: personBar }, age: personAge} = person);
} catch(e) {}

console.log(personName, personBar, personAge);
// Matt, undefined, undefined
```

#### 参数上下文匹配

在函数参数列表中也可以进行解构赋值。对参数的解构赋值不会影响arguments对象，但可以在函数签名中声明在函数体内使用局部变量：

```js
let person = {
  name: 'Matt',
  age: 27
};

function printPerson(foo, {name, age}, bar) {
  console.log(arguments);
  console.log(name, age);
}

function printPerson2(foo, {name: personName, age: personAge}, bar) {
  console.log(arguments);
  console.log(personName, personAge);
}

printPerson('1st', person, '2nd');
// ['1st', { name: 'Matt', age: 27 }, '2nd']
// 'Matt', 27

printPerson2('1st', person, '2nd');
// ['1st', { name: 'Matt', age: 27 }, '2nd']
// 'Matt', 27
```

## 创建对象

虽然使用`Object`构造函数或对象字面量可以方便地创建对象，但这些方式也有明显不足：创建具有同样接口的多个对象需要重复编写很多代码。

### 工厂模式

用于抽象创建特定对象的过程。

```js
function createPerson(name, age, job) {
  let o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function() {
    console.log(this.name);
  };
  return o;
}

let person1 = createPerson("Nicholas", 29, "Software Engineer");
let person2 = createPerson("Greg", 27, "Doctor");
```
!> 这种工厂模式虽然可以解决创建多个类似对象的问题，但没有解决对象标识问题， 即新创建的对象是什么类型。

!> 无法识别构造函数。

### 构造函数模式

```js
function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() {
    console.log(this.name);
  };
}

let person1 = new Person("Nicholas", 29, "Software Engineer");
let person2 = new Person("Greg", 27, "Doctor");

person1.sayName();  // Nicholas
person2.sayName();  // Greg
```

在这个例子中，`Person()`构造函数代替了`createPerson()`工厂函数。实际上，`Person()`内部的代码跟`createPerson()`基本是一样的，只是有如下区别。

+ 没有显式地创建对象。
+ 属性和方法直接赋值给了`this`。
+ 没有`return`。

> 按照惯例，构造函数名称的首字母都是要大写的，非构造函数则以小写字母开头

**要创建`Person`的实例，应使用new操作符。以这种方式调用构造函数会执行如下操作。**

(1) 在内存中创建一个新对象。

(2) 这个新对象内部的`[[Prototype]]`特性被赋值为构造函数的`prototype`属性。

(3) 构造函数内部的`this`被赋值为这个新对象（即`this`指向新对象）。

(4) 执行构造函数内部的代码（给新对象添加属性）。

(5) 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。

定义自定义构造函数可以确保实例被标识为特定类型，相比于工厂模式，这是一个很大的好处。

在这个例子中，person1和person2之所以也被认为是Object的实例，是因为所有自定义对象都继承自Object。

```js
// console.log(person1 instanceof Object);  // true
// console.log(person1 instanceof Person);  // true
// console.log(person2 instanceof Object);  // true
// console.log(person2 instanceof Person);  // true

let Person = function(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() {
    console.log(this.name);
  };
}

let person1 = new Person("Nicholas", 29, "Software Engineer");
let person2 = new Person("Greg", 27, "Doctor");

person1.sayName();  // Nicholas
person2.sayName();  // Greg

console.log(person1 instanceof Object);  // true
console.log(person1 instanceof Person);  // true
console.log(person2 instanceof Object);  // true
console.log(person2 instanceof Person);  // true
```

#### 构造函数也是函数

构造函数与普通函数唯一的区别就是调用方式不同。

除此之外，构造函数也是函数。并没有把某个函数定义为构造函数的特殊语法。

任何函数只要使用new操作符调用就是构造函数，而不使用new操作符调用的函数就是普通函数。

```js
let Person = function(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function() {
    console.log(this.name);
  };
}

// 作为构造函数
let person = new Person("Nicholas", 29, "Software Engineer");
person.sayName();    // "Nicholas"

// 作为函数调用
Person("Greg", 27, "Doctor");   // 添加到window对象
window.sayName();    // "Greg"

// 在另一个对象的作用域中调用
let o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName();   // "Kristen"
```

!> 问题：这种方式创建函数会带来不同的作用域链和标识符解析

!> 虽然解决了对象识别问题,但构造函数体内的方法不是来自同一个对象

 ```js
 console.log(person1.sayName == person2.sayName); // false
 ```

**这个构造函数实际上是这样的：**
 ```js
 function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = new Function("console.log(this.name)"); // 逻辑等价
}
 ```

 **要解决这个问题，可以把函数定义转移到构造函数外部：**
 ```js
 function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = sayName;
}

function sayName() {
  console.log(this.name);
}

let person1 = new Person("Nicholas", 29, "Software Engineer");
let person2 = new Person("Greg", 27, "Doctor");

person1.sayName();  // Nicholas
person2.sayName();  // Greg
 ```

 在这里，`sayName()`被定义在了构造函数外部。在构造函数内部，`sayName`属性等于全局`sayName()`函数。
 
 因为这一次`sayName`属性中包含的只是一个指向外部函数的指针，所以`person1`和`person2`共享了定义在全局作用域上的`sayName()`函数。

 这样虽然解决了相同逻辑的函数重复定义的问题，但全局作用域也因此被搞乱了，因为那个函数实际上只能在一个对象上调用。如果这个对象需要多个方法，那么就要在全局作用域中定义多个函数。这会导致自定义类型引用的代码不能很好地聚集一起。这个新问题可以通过原型模式来解决。

 ### 原型模式

 每个函数都会创建一个`prototype`属性，这个属性是一个对象，包含应该由`特定引用类型`的`实例`共享的`属性`和`方法`。
 
 实际上，这个对象就是通过调用构造函数创建的对象的原型。使用原型对象的好处是，在它上面定义的属性和方法可以被对象实例共享。原来在构造函数中直接赋给对象实例的值，可以直接赋值给它们的原型，如下所示：

 ```js
//  let Person = function() {};
 function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
  console.log(this.name);
};

let person1 = new Person();
person1.sayName(); // "Nicholas"

let person2 = new Person();
person2.sayName(); // "Nicholas"

console.log(person1.sayName == person2.sayName); // true
 ```

!> 这样定义之后，调用构造函数创建的新对象仍然拥有相应的属性和方法。与构造函数模式不同，使用这种原型模式定义的属性和方法是由所有实例共享的。

 ### **理解原型**

只要创建一个函数，就会按照特定的规则为这个函数创建一个`prototype`属性（指向原型对象）。

默认情况下，所有原型对象自动获得一个名为`constructor`的属性，指回与之关联的构造函数。对前面的例子而言，`Person.prototype.constructor`指向`Person`。然后，因构造函数而异，可能会给原型对象添加其他属性和方法。

在自定义构造函数时，原型对象默认只会获得`constructor`属性，其他的所有方法都继承自`Object`。每次调用构造函数创建一个新实例，这个实例的内部`[[Prototype]]`指针就会被赋值为构造函数的原型对象。

脚本中没有访问这个`[[Prototype]]`特性的标准方式，但Firefox、Safari和Chrome会在每个对象上暴露`__proto__`属性，通过这个属性可以访问对象的原型。在其他实现中，这个特性完全被隐藏了。

关键在于理解这一点：实例与构造函数原型之间有直接的联系，但实例与构造函数之间没有。

```js
/**
 * 构造函数可以是函数表达式
 * 也可以是函数声明，因此以下两种形式都可以：
 *   function Person() {}
 *   let Person = function() {}
 */
function Person() {}

/**
 * 声明之后，构造函数就有了一个
 * 与之关联的原型对象：
 */
console.log(typeof Person.prototype);
console.log(Person.prototype);
// {
//   constructor: f Person(),
//   __proto__: Object
// }

/**
 * 如前所述，构造函数有一个prototype属性
 * 引用其原型对象，而这个原型对象也有一个
 * constructor属性，引用这个构造函数
 * 换句话说，两者循环引用：
 */
console.log(Person.prototype.constructor === Person); // true

/**
 * 正常的原型链都会终止于Object的原型对象
 * Object原型的原型是null
 */
console.log(Person.prototype.__proto__ === Object.prototype);   // true
console.log(Person.prototype.__proto__.constructor === Object); // true
console.log(Person.prototype.__proto__.__proto__ === null);     // true

console.log(Person.prototype.__proto__);
// {
//   constructor: f Object(),
//   toString: ...
//   hasOwnProperty: ...
//   isPrototypeOf: ...
//   ...
// }
　
　
let person1 = new Person(),
    person2 = new Person();

/**
 * 构造函数、原型对象和实例
 * 是3个完全不同的对象：
 */
console.log(person1 !== Person);           // true
console.log(person1 !== Person.prototype); // true
console.log(Person.prototype !== Person);  // true

/**
  * 实例通过__proto__链接到原型对象，
  * 它实际上指向隐藏特性[[Prototype]]
  *
  * 构造函数通过prototype属性链接到原型对象
  *
  * 实例与构造函数没有直接联系，与原型对象有直接联系
  */
console.log(person1.__proto__ === Person.prototype);   // true
conosle.log(person1.__proto__.constructor === Person); // true

/**
 * 同一个构造函数创建的两个实例
 * 共享同一个原型对象：
 */
console.log(person1.__proto__ === person2.__proto__); // true

/**
 * instanceof检查实例的原型链中
 * 是否包含指定构造函数的原型：
 */
console.log(person1 instanceof Person);           // true
console.log(person1 instanceof Object);           // true
console.log(Person.prototype instanceof Object);  // true
```
![](../assets/image/javascript/class__创建对象__理解原型01.png ':size=80%')

**ECMAScript的`Object`类型有一个方法叫`Object.getPrototypeOf()`，返回参数的内部特性`[[Prototype]]`的值。**
```js
console.log(Object.getPrototypeOf(person1) == Person.prototype);  // true
console.log(Object.getPrototypeOf(person1).name);                 // "Nicholas"
```

第一行代码简单确认了Object.getPrototypeOf()返回的对象就是传入对象的原型对象。第二行代码则取得了原型对象上name属性的值，即"Nicholas"。使用Object.getPrototypeOf()可以方便地取得一个对象的原型，而这在通过原型实现继承时显得尤为重要。

**`Object`类型还有一个`setPrototypeOf()`方法，可以向实例的私有特性`[[Prototype]]`写入一个新值。这样就可以重写一个对象的原型继承关系：**

```js
let biped = {
  numLegs: 2
};
let person = {
  name: 'Matt'
};

Object.setPrototypeOf(person, biped);

console.log(person.name);                              // Matt
console.log(person.numLegs);                           // 2
console.log(Object.getPrototypeOf(person) === biped);  // true
```

!> 警告　`Object.setPrototypeOf()`可能会严重影响代码性能。Mozilla文档说得很清楚：“在所有浏览器和JavaScript引擎中，修改继承关系的影响都是微妙且深远的。这种影响并不仅是执行`Object.setPrototypeOf()`语句那么简单，而是会涉及所有访问了那些修改过`[[Prototype]]`的对象的代码。”

**为避免使用`Object.setPrototypeOf()`可能造成的性能下降，可以通过`Object.create()`来创建一个新对象，同时为其指定原型：**

```js
let biped = {
  numLegs: 2
};
let person = Object.create(biped);
person.name = 'Matt';

console.log(person.name);                              // Matt
console.log(person.numLegs);                           // 2
console.log(Object.getPrototypeOf(person) === biped);  // true
```

#### 原型层级

在通过对象访问属性时，会按照这个属性的名称开始搜索。搜索开始于对象实例本身。如果在这个实例上发现了给定的名称，则返回该名称对应的值。如果没有找到这个属性，则搜索会沿着指针进入原型对象，然后在原型对象上找到属性后，再返回对应的值。因此，在调用`person1.sayName()`时，会发生两步搜索。首先，JavaScript引擎会问：“`person1`实例有`sayName`属性吗？”答案是没有。然后，继续搜索并问：“`person1`的原型有`sayName`属性吗？”答案是有。于是就返回了保存在原型上的这个函数。在调用`person2.sayName()`时，会发生同样的搜索过程，而且也会返回相同的结果。这就是原型用于在多个对象实例间共享属性和方法的原理。

!> 注意　前面提到的`constructor`属性只存在于原型对象，因此通过实例对象也是可以访问到的。

```js
function Person() {}

Person.prototype.name = "Nicholas";

let person1 = new Person();
let person2 = new Person();

person1.name = "Greg";
console.log(person1.name);  // "Greg"，来自实例
console.log(person2.name);  // "Nicholas"，来自原型

delete person1.name;
console.log(person1.name);  // "Nicholas"，来自原型
```

在这个例子中，`person1`的`name`属性遮蔽了原型对象上的同名属性。虽然`person1.name`和`person2.name`都返回了值，但前者返回的是`"Greg"`（来自实例），后者返回的是`"Nicholas"`（来自原型）。

当`console.log()`访问`person1.name`时，会先在实例上搜索个属性。因为这个属性在实例上存在，所以就不会再搜索原型对象了。而在访问`person2.name`时，并没有在实例上找到这个属性，所以会继续搜索原型对象并使用定义在原型上的属性。

只要给对象实例添加一个属性，这个属性就会**遮蔽（shadow）**原型对象上的同名属性，也就是虽然不会修改它，但会屏蔽对它的访问。

即使在实例上把这个属性设置为`null`，也不会恢复它和原型的联系。不过，使用`delete`操作符可以完全删除实例上的这个属性，从而让标识符解析过程能够继续搜索原型对象。

**`hasOwnProperty()`方法用于确定某个属性是在实例上还是在原型对象上。这个方法是继承自`Object`的，会在属性存在于调用它的对象实例上时返回`true`，如下面的例子所示：**

```js
function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
  console.log(this.name);
};

let person1 = new Person();
let person2 = new Person();
console.log(person1.hasOwnProperty("name")); // false

person1.name = "Greg";
console.log(person1.name); // "Greg"，来自实例
console.log(person1.hasOwnProperty("name")); // true

console.log(person2.name); // "Nicholas"，来自原型
console.log(person2.hasOwnProperty("name")); // false

delete person1.name;
console.log(person1.name); // "Nicholas"，来自原型
console.log(person1.hasOwnProperty("name")); // false
```

在这个例子中，通过调用`hasOwnProperty()`能够清楚地看到访问的是实例属性还是原型属性。

调用`person1.hasOwnProperty("name")`只在重写`person1`上`name`属性的情况下才返回`true`，表明此时`name`是一个实例属性，不是原型属性。

![](../assets/image/javascript/class__创建对象__理解原型02.png ':size=80%')

!> 注意　ECMAScript的`Object.getOwnPropertyDescriptor()`方法只对实例属性有效。要取得原型属性的描述符，就必须直接在原型对象上调用`Object.getOwnPropertyDescriptor()`。

#### 原型和in操作符

有两种方式使用in操作符：单独使用和在for-in循环中使用。在单独使用时，in操作符会在可以通过对象访问指定属性时返回true，无论该属性是在实例上还是在原型上。

```js
function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
  console.log(this.name);
};

let person1 = new Person();
let person2 = new Person();

console.log(person1.hasOwnProperty("name")); // false
console.log("name" in person1); // true

person1.name = "Greg";
console.log(person1.name); // "Greg"，来自实例
console.log(person1.hasOwnProperty("name")); // true
console.log("name" in person1); // true

console.log(person2.name); // "Nicholas"，来自原型
console.log(person2.hasOwnProperty("name")); // false
console.log("name" in person2); // true

delete person1.name;
console.log(person1.name); // "Nicholas"，来自原型
console.log(person1.hasOwnProperty("name")); // false
console.log("name" in person1); // true
```

只要通过对象可以访问，`in`操作符就返回`true`，而`hasOwnProperty()`只有属性存在于实例上时才返回`true`。

因此，只要`in`操作符返回`true`且`hasOwnProperty()`返回`false`，就说明该属性是一个原型属性。

```js
function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
  console.log(this.name);
};

let person = new Person();
console.log(hasPrototypeProperty(person, "name")); // true

person.name = "Greg";
console.log(hasPrototypeProperty(person, "name")); // false
```

在这里，`name`属性首先只存在于原型上，所以`hasPrototypeProperty`()返回`true`。而在实例上重写这个属性后，实例上也有了这个属性，因此`hasPrototypeProperty()`返回`false`。即便此时原型对象还有`name`属性，但因为实例上的属性遮蔽了它，所以不会用到。

在`for-in`循环中使用in操作符时，可以通过对象访问且可以被枚举的属性都会返回，包括实例属性和原型属性。遮蔽原型中不可枚举（`[[Enumerable]]`特性被设置为`false`）属性的实例属性也会在`for-in`循环中返回，因为默认情况下开发者定义的属性都是可枚举的。

要获得对象上所有可枚举的实例属性，可以使用`Object.keys()`方法。这个方法接收一个对象作为参数，返回包含该对象所有`可枚举`属性名称的字符串数组。比如：

```js
function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
  console.log(this.name);
};

let keys = Object.keys(Person.prototype);
console.log(keys);   // "name,age,job,sayName"

let p1 = new Person();
p1.name = "Rob";
p1.age = 31;
let p1keys = Object.keys(p1);
console.log(p1keys); // "[name,age]"
```

如果想列出所有实例属性，无论是否可以枚举，都可以使用`Object.getOwnPropertyNames()`：

```js
let keys = Object.getOwnPropertyNames(Person.prototype);
console.log(keys);   // "[constructor,name,age,job,sayName]"
```

在ECMAScript 6新增符号类型之后，相应地出现了增加一个`Object.getOwnPropertyNames()`的兄弟方法的需求，因为以符号为键的属性没有名称的概念。因此，`Object.getOwnPropertySymbols()`方法就出现了，这个方法与`Object.getOwnPropertyNames()`类似，只是针对符号而已：

```js
let k1 = Symbol('k1'),
    k2 = Symbol('k2');

let o = {
  [k1]: 'k1',
  [k2]: 'k2'
};

console.log(Object.getOwnPropertySymbols(o));
// [Symbol(k1), Symbol(k2)]
```

#### 属性枚举顺序

`for-in`循环、`Object.keys()`、`Object.getOwnPropertyNames()`、`Object.getOwnPropertySymbols()`以及`Object.assign()`在属性枚举顺序方面有很大区别。`for-in`循环和`Object.keys()`的枚举顺序是不确定的，取决于JavaScript引擎，可能因浏览器而异。

`Object.getOwnPropertyNames()`、`Object.getOwnPropertySymbols()`和`Object.assign()`的枚举顺序是确定性的。先以升序枚举数值键，然后以插入顺序枚举字符串和符号键。在对象字面量中定义的键以它们逗号分隔的顺序插入。

```js
let k1 = Symbol('k1'),
    k2 = Symbol('k2');

let o = {
  1: 1,
  first: 'first',
  [k1]: 'sym2',
  second: 'second',
  0: 0
};

o[k2] = 'sym2';
o[3] = 3;
o.third = 'third';
o[2] = 2;

console.log(Object.getOwnPropertyNames(o));
// ["0", "1", "2", "3", "first", "second", "third"]

console.log(Object.getOwnPropertySymbols(o));
// [Symbol(k1), Symbol(k2)]
```

### 对象迭代  

ECMAScript 2017新增了两个静态方法，用于将对象内容转换为序列化的——更重要的是可迭代的——格式。这两个静态方法`Object.values()`和`Object.entries()`接收一个对象，返回它们内容的数组。

**`Object.values()`返回对象值的数组，`Object.entries()`返回键/值对的数组。**

```js
const sym = Symbol();
const o = {
  foo: 'bar',
  baz: 1,
  qux: {},
  [sym]: 'foo'
};

console.log(Object.values(o));
// ["bar", 1, {}]

console.log(Object.entries((o)));
// [["foo", "bar"], ["baz", 1], ["qux", {}]]

```

!> 注意，非字符串属性会被转换为字符串输出，符号属性会被忽略

这两个方法执行对象的浅复制：

```js
const o = {
  qux: {}
};

console.log(Object.values(o)[0] === o.qux);
// true

console.log(Object.entries(o)[0][1] === o.qux);
// true
```

#### 其他原型语法

直接通过一个包含所有属性和方法的对象字面量来重写原型

```js
function Person() {}

Person.prototype = {
  name: "Nicholas",
  age: 29,
  job: "Software Engineer",
  sayName() {
    console.log(this.name);
  }
};
```

在这个例子中，`Person.prototype`被设置为等于一个通过对象字面量创建的新对象。

最终结果是一样的，只有一个问题：这样重写之后，`Person.prototype`的`constructor`属性就不指向`Person`了。在创建函数时，也会创建它的`prototype`对象，同时会自动给这个原型的`constructor`属性赋值。

而上面的写法完全重写了默认的`prototype`对象，因此其`constructor`属性也指向了完全不同的新对象（`Object`构造函数），不再指向原来的构造函数。虽然`instanceof`操作符还能可靠地返回值，但我们不能再依靠`constructor`属性来识别类型了

如下面的例子所示：
```js
let friend = new Person();

console.log(friend instanceof Object);      // true
console.log(friend instanceof Person);      // true
console.log(friend.constructor == Person);  // false
console.log(friend.constructor == Object);  // true
```

这里，`instanceof`仍然对`Object`和`Person`都返回`true`。但`constructor`属性现在等于`Object`而不是`Person`了。如果`constructor`的值很重要，则可以像下面这样在重写原型对象时专门设置一下它的值：

```js
function Person() {
}

Person.prototype = {
  constructor: Person,
  name: "Nicholas",
  age: 29,
  job: "Software Engineer",
  sayName() {
    console.log(this.name);
  }
};
```
> 以这种方式恢复`constructor`属性会创建一个`[[Enumerable]]`为true的属性。而原生`constructor`属性默认是不可枚举的。

因此，如果你使用的是兼容ECMAScript的JavaScript引擎，那可能会改为使用`Object.defineProperty()`方法来定义`constructor`属性：

```js
function Person() {}

Person.prototype = {
  name: "Nicholas",
  age: 29,
  job: "Software Engineer",
  sayName() {
    console.log(this.name);
  }
};

// 恢复constructor属性
Object.defineProperty(Person.prototype, "constructor", {
  enumerable: false, // 可不填，默认为false
  value: Person
});
```

#### 原型的动态性

因为从原型上搜索值的过程是动态的，所以即使实例在修改原型之前已经存在，任何时候对原型对象所做的修改也会在实例上反映出来.

```js
let friend = new Person();

Person.prototype.sayHi = function() {
  console.log("hi");
};

friend.sayHi();   // "hi"，没问题！
```

在调用`friend.sayHi()`时，首先会从这个实例中搜索名为`sayHi`的属性。在没有找到的情况下，运行时会继续搜索原型对象。因为实例和原型之间的链接就是简单的指针，而不是保存的副本，所以会在原型上找到`sayHi`属性并返回这个属性保存的函数。

**虽然随时能给原型添加属性和方法，并能够立即反映在所有对象实例上，但这跟重写整个原型是两回事。**实例的`[[Prototype]]`指针是在调用构造函数时自动赋值的，这个指针即使把原型修改为不同的对象也不会变。重写整个原型会切断最初原型与构造函数的联系，但实例引用的仍然是最初的原型。**实例只有指向原型的指针，没有指向构造函数的指针**。

```js
function Person() {}

let friend = new Person();
Person.prototype = {
  constructor: Person,
  name: "Nicholas",
  age: 29,
  job: "Software Engineer",
  sayName() {
    console.log(this.name);
  }
};

friend.sayName();  // 错误
```

在这个例子中，`Person`的新实例是在重写原型对象之前创建的。在调用`friend.sayName()`的时候，会导致错误。这是因为`firend`指向的原型还是最初的原型，而这个原型上并没有`sayName`属性。重写构造函数上的原型之后再创建的实例才会引用新的原型。而在此之前创建的实例仍然会引用最初的原型。

![](../assets/image/javascript/class__创建对象__对象迭代01.png ':size=80%')

#### 原生对象原型

原型模式之所以重要，不仅体现在自定义类型上，而且还因为它也是实现所有原生引用类型的模式。所有原生引用类型的构造函数（包括`Object`、`Array`、`String`等）都在原型上定义了实例方法。

比如，数组实例的`sort()`方法就是`Array.prototype`上定义的，而字符串包装对象的`substring()`方法也是在`String.prototype`上定义的。

通过原生对象的原型可以取得所有默认方法的引用，也可以给原生类型的实例定义新的方法。可以像修改自定义对象原型一样修改原生对象原型，因此随时可以添加方。

```js
console.log(typeof Array.prototype.sort);       // "function"
console.log(typeof String.prototype.substring); // "function"

String.prototype.startsWith = function (text) {
  return this.indexOf(text) === 0;
};

let msg = "Hello world!";
console.log(msg.startsWith("Hello"));  // true
```

!> 注意　尽管可以这么做，但并不推荐在产品环境中修改原生对象原型。这样做很可能造成误会，而且可能引发命名冲突（比如一个名称在某个浏览器实现中不存在，在另一个实现中却存在）。另外还有可能意外重写原生的方法。推荐的做法是创建一个自定义的类，继承原生类型。

#### 原型的问题

原型模式也不是没有问题

+ **它弱化了向构造函数传递初始化参数的能力，会导致所有实例默认都取得相同的属性值**
+ **原型的最主要问题源自它的共享特性**

原型上的所有属性是在实例间共享的，这对函数来说比较合适。另外包含原始值的属性也还好，可以通过在实例上添加同名属性来简单地遮蔽原型上的属性。真正的问题来自包含引用值的属性。

```js
function Person() {}

Person.prototype = {
  constructor: Person,
  name: "Nicholas",
  age: 29,
  job: "Software Engineer",
  friends: ["Shelby", "Court"],
  sayName() {
    console.log(this.name);
  }
};

let person1 = new Person();
let person2 = new Person();

person1.friends.push("Van");

console.log(person1.friends);  // "Shelby,Court,Van"
console.log(person2.friends);  // "Shelby,Court,Van"
console.log(person1.friends === person2.friends);  // true
```

这里，`Person.prototype`有一个名为`friends`的属性，它包含一个字符串数组。然后这里创建了两个`Person`的实例。`person1.friends`通过`push`方法向数组中添加了一个字符串。由于这个`friends`属性存在于`Person.prototype`而非`person1`上，新加的这个字符串也会在（指向同一个数组的）`person2.friends`上反映出来。如果这是有意在多个实例间共享数组，那没什么问题。但一般来说，不同的实例应该有属于自己的属性副本。这就是实际开发中通常不单独使用原型模式的原因。

## 继承

很多面向对象语言都支持两种继承：`接口继承`和`实现继承`。前者只继承方法签名，后者继承实际的方法。接口继承在ECMAScript中是不可能的，因为函数没有签名。实现继承是ECMAScript唯一支持的继承方式，而这主要是通过原型链实现的。

### 原型链

ECMA-262把原型链定义为ECMAScript的主要继承方式。其基本思想就是通过原型继承多个引用类型的属性和方法。重温一下构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型有一个属性指回构造函数，而实例有一个内部指针指向原型。如果原型是另一个类型的实例呢？那就意味着这个原型本身有一个内部指针指向另一个原型，相应地另一个原型也有一个指针指向另一个构造函数。这样就在实例和原型之间构造了一条原型链。这就是原型链的基本构想。

```js
function SuperType() {
  this.property = true;
}

SuperType.prototype.getSuperValue = function() {
  return this.property;
};

function SubType() {
  this.subproperty = false;
}

// 继承SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function () {
  return this.subproperty;
};

let instance = new SubType();
console.log(instance.getSuperValue()); // true
```

以上代码定义了两个类型：`SuperType`和`SubType`。这两个类型分别定义了一个属性和一个方法。这两个类型的主要区别是`SubType`通过创建`SuperType`的实例并将其赋值给自己的原型`SubTtype.prototype`实现了对`SuperType`的继承。这个赋值重写了`SubType`最初的原型，将其替换为`SuperType`的实例。这意味着`SuperType`实例可以访问的所有属性和方法也会存在于`SubType.prototype`。这样实现继承之后，代码紧接着又给`SubType.prototype`，也就是这个`SuperType`的实例添加了一个新方法。最后又创建了`SubType`的实例并调用了它继承的`getSuperValue()`方法。

![](../assets/image/javascript/class__继承__原型链01.png ':size=80%')

这个例子中实现继承的关键，是`SubType`没有使用默认原型，而是将其替换成了一个新的对象。这个新的对象恰好是`SuperType`的实例。这样一来，`SubType`的实例不仅能从`SuperType`的实例中继承属性和方法，而且还与`SuperType`的原型挂上了钩。于是`instance`（通过内部的`[[Prototype]]`）指向`SubType.prototype`，而`SubType.prototype`（作为`SuperType`的实例又通过内部的`[[Prototype]]`）指向`SuperType.prototype`。注意，`getSuperValue()`方法还在`SuperType.prototype`对象上，而`property`属性则在`SubType.prototype上`。这是因为`getSuperValue()`是一个原型方法，而`property`是一个实例属性。`SubType.prototype`现在是`SuperType`的一个实例，因此`property`才会存储在它上面。还要注意，由于`SubType.prototype`的`constructor`属性被重写为指向`SuperType`，所以`instance.constructor`也指向`SuperType`。

原型链扩展了前面描述的原型搜索机制。我们知道，在读取实例上的属性时，首先会在实例上搜索这个属性。如果没找到，则会继承搜索实例的原型。在通过原型链实现继承之后，搜索就可以继承向上，搜索原型的原型。对前面的例子而言，调用`instance.getSuperValue()`经过了3步搜索：`instance`、`SubType.prototype`和`SuperType.prototype`，最后一步才找到这个方法。对属性和方法的搜索会一直持续到原型链的末端。

#### 默认原型

实际上，原型链中还有一环。默认情况下，所有引用类型都继承自`Object`，这也是通过原型链实现的。任何函数的默认原型都是一个`Object`的实例，这意味着这个实例有一个内部指针指向`Object.prototype`。这也是为什么自定义类型能够继承包括`toString()`、`valueOf()`在内的所有默认方法的原因。因此前面的例子还有额外一层继承关系。

![](../assets/image/javascript/class__继承__原型链02.png ':size=80%')

`SubType`继承`SuperType`，而`SuperType`继承`Object`。在调用`instance.toString()`时，实际上调用的是保存在`Object.prototype`上的方法。

#### 原型与继承关系

原型与实例的关系可以通过两种方式来确定。

+ 第一种方式是使用`instanceof`操作符，如果一个实例的原型链中出现过相应的构造函数，则`instanceof`返回`true`。如下例所示：

```js
console.log(instance instanceof Object);     // true
console.log(instance instanceof SuperType);  // true
console.log(instance instanceof SubType);    // true
```

+ 第二种方式是使用`isPrototypeOf()`方法。原型链中的每个原型都可以调用这个方法，如下例所示，只要原型链中包含这个原型，这个方法就返回`true`：

```js
console.log(Object.prototype.isPrototypeOf(instance));     // true
console.log(SuperType.prototype.isPrototypeOf(instance));  // true
console.log(SubType.prototype.isPrototypeOf(instance));    // true
```

#### 关于方法

子类有时候需要覆盖父类的方法，或者增加父类没有的方法。为此，这些方法必须在原型赋值之后再添加到原型上

```js
function SuperType() {
  this.property = true;
}

SuperType.prototype.getSuperValue = function() {
  return this.property;
};

function SubType() {
  this.subproperty = false;
}

// 继承SuperType
SubType.prototype = new SuperType();

// 新方法
SubType.prototype.getSubValue = function () {
  return this.subproperty;
};

// 覆盖已有的方法
SubType.prototype.getSuperValue = function () {
  return false;
};

let instance = new SubType();
console.log(instance.getSuperValue()); // false
```
第一个方法`getSubValue()`是`SubType`的新方法，而第二个方法`getSuperValue()`是原型链上已经存在但在这里被遮蔽的方法。后面在`SubType`实例上调用`getSuperValue()`时调用的是这个方法。而`SuperType`的实例仍然会调用最初的方法。重点在于上述两个方法都是在把原型赋值为`SuperType`的实例之后定义的。

另一个要理解的重点是，以对象字面量方式创建原型方法会破坏之前的原型链，因为这相当于重写了原型链。下面是一个例子：

```js
function SuperType() {
  this.property = true;
}

SuperType.prototype.getSuperValue = function() {
  return this.property;
};

function SubType() {
  this.subproperty = false;
}

// 继承SuperType
SubType.prototype = new SuperType();

// 通过对象字面量添加新方法，这会导致上一行无效
SubType.prototype = {
  getSubValue() {
    return this.subproperty;
  },

  someOtherMethod() {
    return false;
  }
};

let instance = new SubType();
console.log(instance.getSuperValue()); // 出错！
```

在这段代码中，子类的原型在被赋值为`SuperType`的实例后，又被一个对象字面量覆盖了。覆盖后的原型是一个`Object`的实例，而不再是`SuperType`的实例。因此之前的原型链就断了。`SubType`和`SuperType`之间也没有关系了。

#### 原型链的问题

原型链虽然是实现继承的强大工具，但它也有问题。主要问题出现在原型中包含引用值的时候。前面在谈到原型的问题时也提到过，原型中包含的引用值会在所有实例间共享，这也是为什么属性通常会在构造函数中定义而不会定义在原型上的原因。在使用原型实现继承时，原型实际上变成了另一个类型的实例。这意味着原先的实例属性摇身一变成为了原型属性。下面的例子揭示了这个问题：

```js
function SuperType() {
  this.colors = ["red", "blue", "green"];
}

function SubType() {}

// 继承SuperType
SubType.prototype = new SuperType();

let instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"

let instance2 = new SubType();
console.log(instance2.colors); // "red,blue,green,black"
```

在这个例子中，`SuperType`构造函数定义了一个`colors`属性，其中包含一个数组（引用值）。每个`SuperType`的实例都会有自己的`colors`属性，包含自己的数组。但是，当`SubType`通过原型继承`SuperType`后，`SubType.prototype`变成了`SuperType`的一个实例，因而也获得了自己的`colors`属性。这类似于创建了`SubType.prototype.colors`属性。最终结果是，`SubType`的所有实例都会共享这个`colors`属性。这一点通过`instance1.colors`上的修改也能反映到`instance2.colors`上就可以看出来。

原型链的第二个问题是，子类型在实例化时不能给父类型的构造函数传参。事实上，我们无法在不影响所有对象实例的情况下把参数传进父类的构造函数。再加上之前提到的原型中包含引用值的问题，就导致原型链基本不会被单独使用。

### **盗用构造函数--apply()、call()**

为了解决原型包含引用值导致的继承问题，一种叫作`“盗用构造函数”（constructor stealing）`的技术在开发社区流行起来（这种技术有时也称作`“对象伪装”`或`“经典继承”`）。基本思路很简单：在子类构造函数中调用父类构造函数。因为毕竟函数就是在特定上下文中执行代码的简单对象，所以可以使用`apply()`和`call()`方法以新创建的对象为上下文执行构造函数。 

```js
function SuperType() {
  this.colors = ["red", "blue", "green"];
}

function SubType() {
  // 继承SuperType
  SuperType.call(this);
}

let instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); // "red,blue,green,black"

let instance2 = new SubType();
console.log(instance2.colors); // "red,blue,green"
```

通过使用`call()`（或`apply()`）方法，`SuperType`构造函数在为`SubType`的实例创建的新对象的上下文中执行了。这相当于新的`SubType`对象上运行了`SuperType()`函数中的所有初始化代码。结果就是每个实例都会有自己的`colors`属性。(更多看[函数(Function)##apply()和call()](javascript/Function.md?id=apply()和call()))

#### 传递参数

相比于使用原型链，盗用构造函数的一个优点就是可以在子类构造函数中向父类构造函数传参。

```js
function SuperType(name){
  this.name = name;
}

function SubType() {
  // 继承SuperType并传参
  SuperType.call(this, "Nicholas");

  // 实例属性
  this.age = 29;
}

let instance = new SubType();
console.log(instance.name); // "Nicholas";
console.log(instance.age);  // 29
```
在这个例子中，`SuperType`构造函数接收一个参数`name`，然后将它赋值给一个属性。在`SubType`构造函数中调用`SuperType`构造函数时传入这个参数，实际上会在`SubType`的实例上定义name属性。为确保`SuperType`构造函数不会覆盖`SubType`定义的属性，可以在调用父类构造函数之后再给子类实例添加额外的属性。

#### 盗用构造函数的问题

盗用构造函数的主要缺点，也是使用构造函数模式自定义类型的问题：必须在构造函数中定义方法，因此函数不能重用。此外，子类也不能访问父类原型上定义的方法，因此所有类型只能使用构造函数模式。由于存在这些问题，盗用构造函数基本上也不能单独使用。

### 组合继承

组合继承（有时候也叫伪经典继承）综合了原型链和盗用构造函数，将两者的优点集中了起来。基本的思路是使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性。这样既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性。来看下面的例子：

```js
function SuperType(name){
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
  console.log(this.name);
};

function SubType(name, age){
  // 继承属性
  SuperType.call(this, name);

  this.age = age;
}

// 继承方法
SubType.prototype = new SuperType();

SubType.prototype.sayAge = function() {
  console.log(this.age);
};

let instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
console.log(instance1.colors);  // "red,blue,green,black"
instance1.sayName();            // "Nicholas";
instance1.sayAge();             // 29

let instance2 = new SubType("Greg", 27);
console.log(instance2.colors);  // "red,blue,green"
instance2.sayName();            // "Greg";
instance2.sayAge();             // 27
```

在这个例子中，`SuperType`构造函数定义了两个属性，`name`和`colors`，而它的原型上也定义了一个方法叫`sayName()`。`SubType`构造函数调用了`SuperType`构造函数，传入了`name`参数，然后又定义了自己的属性`age`。此外，`SubType.prototype`也被赋值为`SuperType`的实例。原型赋值之后，又在这个原型上添加了新方法`sayAge()`。这样，就可以创建两个`SubType`实例，让这两个实例都有自己的属性，包括`colors`，同时还共享相同的方法。

组合继承弥补了原型链和盗用构造函数的不足，是`JavaScript`中使用最多的继承模式。而且组合继承也保留了`instanceof`操作符和`isPrototypeOf()`方法识别合成对象的能力。

### 原型式继承

2006年，Douglas Crockford写了一篇文章：《JavaScript中的原型式继承》（“Prototypal Inheritance in JavaScript”）。这篇文章介绍了一种不涉及严格意义上构造函数的继承方法。他的出发点是即使不自定义类型也可以通过原型实现对象之间的信息共享。文章最终给出了一个函数：

```js
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
```

这个`object()`函数会创建一个临时构造函数，将传入的对象赋值给这个构造函数的原型，然后返回这个临时类型的一个实例。本质上，`object()`是对传入的对象执行了一次浅复制。
```js
let person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

let anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

let yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

console.log(person.friends);  // "Shelby,Court,Van,Rob,Barbie"
```

Crockford推荐的原型式继承适用于这种情况：你有一个对象，想在它的基础上再创建一个新对象。你需要把这个对象先传给`object()`，然后再对返回的对象进行适当修改。在这个例子中，`person`对象定义了另一个对象也应该共享的信息，把它传给`object()`之后会返回一个新对象。这个新对象的原型是`person`，意味着它的原型上既有原始值属性又有引用值属性。这也意味着`person.friends`不仅是`person`的属性，也会跟`anotherPerson`和`yetAnotherPerson`共享。这里实际上克隆了两个`person`。

ECMAScript 5通过增加`Object.create()`方法将原型式继承的概念规范化了。这个方法接收两个参数：作为新对象原型的对象，以及给新对象定义额外属性的对象（第二个可选）。在只有一个参数时，`Object.create()`与这里的`object()`��法效果相同：

```js
let person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

let anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

let yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

console.log(person.friends);  // "Shelby,Court,Van,Rob,Barbie"
```

`Object.create()`的第二个参数与`Object.defineProperties()`的第二个参数一样：每个新增属性都通过各自的描述符来描述。以这种方式添加的属性会遮蔽原型对象上的同名属性。比如：

```js
let person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

let anotherPerson = Object.create(person, {
  name: {
    value: "Greg"
  }
});
console.log(anotherPerson.name);  // "Greg"
```

原型式继承非常适合不需要单独创建构造函数，但仍然需要在对象间共享信息的场合。但要记住，属性中包含的引用值始终会在相关对象间共享，跟使用原型模式是一样的。

### 寄生式继承

与原型式继承比较接近的一种继承方式是寄生式继承（parasitic inheritance），也是Crockford首倡的一种模式。寄生式继承背后的思路类似于寄生构造函数和工厂模式：创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象。基本的寄生继承模式如下：
```js
function createAnother(original){
  let clone = object(original);  // 通过调用函数创建一个新对象
  clone.sayHi = function() {     // 以某种方式增强这个对象
    console.log("hi");
  };
  return clone;           // 返回这个对象
}

let person = {
  name: "Nicholas",
  friends: ["Shelby", "Court", "Van"]
};

let anotherPerson = createAnother(person);
anotherPerson.sayHi();  // "hi"
```

寄生式继承同样适合主要关注对象，而不在乎类型和构造函数的场景。`object()`函数不是寄生式继承所必需的，任何返回新对象的函数都可以在这里使用。

!> 注意　通过寄生式继承给对象添加函数会导致函数难以重用，与构造函数模式类似。

#### 寄生式组合继承

组合继承其实也存在效率问题。最主要的效率问题就是父类构造函数始终会被调用两次：一次在是创建子类原型时调用，另一次是在子类构造函数中调用。本质上，子类原型最终是要包含超类对象的所有实例属性，子类构造函数只要在执行时重写自己的原型就行了。再来看一看这个组合继承的例子：

```js
function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
  console.log(this.name);
};

function SubType(name, age){
  SuperType.call(this, name);   // 第二次调用SuperType()
  this.age = age;
}

SubType.prototype = new SuperType();   // 第一次调用SuperType()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function() {
  console.log(this.age);
};    
```

在上面的代码执行后，`SubType.prototype`上会有两个属性：`name`和`colors`。它们都是`SuperType`的实例属性，但现在成为了`SubType`的原型属性。在调用`SubType`构造函数时，也会调用`SuperType`构造函数，这一次会在新对象上创建实例属性`name`和`colors`。这两个实例属性会遮蔽原型上同名的属性。

![](../assets/image/javascript/class__继承__寄生式继承01.png ':size=80%')

有两组name和colors属性：一组在实例上，另一组在SubType的原型上。这是调用两次SuperType构造函数的结果。

解决这个问题办法。

寄生式组合继承通过盗用构造函数继承属性，但使用混合式原型链继承方法。基本思路是不通过调用父类构造函数给子类原型赋值，而是取得父类原型的一个副本。说到底就是使用寄生式继承来继承父类原型，然后将返回的新对象赋值给子类原型。寄生式组合继承的基本模式如下所示：

```js
function inheritPrototype(subType, superType) {
  let prototype = object(superType.prototype);  // 创建对象
  prototype.constructor = subType;              // 增强对象
  subType.prototype = prototype;                // 赋值对象
}
```

这个`inheritPrototype()`函数实现了寄生式组合继承的核心逻辑。这个函数接收两个参数：子类构造函数和父类构造函数。在这个函数内部，第一步是创建父类原型的一个副本。然后，给返回的`prototype`对象设置`constructor`属性，解决由于重写原型导致默认`constructor`丢失的问题。最后将新创建的对象赋值给子类型的原型。如下例所示，调用`inheritPrototype()`就可以实现前面例子中的子类型原型赋值：

```js
function SuperType(name) {
  this.name = name;
  this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
  console.log(this.name);
};

function SubType(name, age) {
  SuperType.call(this, name);

  this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function() {
  console.log(this.age);
};
```

这里只调用了一次`SuperType`构造函数，避免了`SubType.prototype`上不必要也用不到的属性，因此可以说这个例子的效率更高。而且，原型链仍然保持不变，因此`instanceof`操作符和`isPrototypeOf()`方法正常有效。寄生式组合继承可以算是引用类型继承的最佳模式。

## **类**

虽然ECMAScript 6类表面上看起来可以支持正式的面向对象编程，但实际上它背后使用的仍然是原型和构造函数的概念。

### 类定义

与函数类型相似，定义类也有两种主要方式：类声明和类表达式。

```js
// 类声明
class Person {}

// 类表达式
const Animal = class {};
```

与函数表达式类似，类表达式在它们被求值前也不能引用。不过，与函数定义不同的是，虽然函数声明可以提升，但类定义不能：

```js
console.log(FunctionExpression);   // undefined
var FunctionExpression = function() {};
console.log(FunctionExpression);   // function() {}

console.log(FunctionDeclaration);  // FunctionDeclaration() {}
function FunctionDeclaration() {}
console.log(FunctionDeclaration);  // FunctionDeclaration() {}

console.log(ClassExpression);      // undefined
var ClassExpression = class {};
console.log(ClassExpression);      // class {}

console.log(ClassDeclaration);     // ReferenceError: ClassDeclaration is not defined
class ClassDeclaration {}
console.log(ClassDeclaration);     // class ClassDeclaration {}
```

另一个跟函数声明不同的地方是，函数受函数作用域限制，而类受块作用域限制：

```js
{
  function FunctionDeclaration() {}
  class ClassDeclaration {}
}

console.log(FunctionDeclaration); // FunctionDeclaration() {}
console.log(ClassDeclaration);    // ReferenceError: ClassDeclaration is not defined
```

#### 类的构成

类可以包含构造函数方法、实例方法、获取函数、设置函数和静态类方法，但这些都不是必需的。空的类定义照样有效。默认情况下，类定义中的代码都在严格模式下执行。

与函数构造函数一样，多数编程风格都建议类名的首字母要大写，以区别于通过它创建的实例（比如，通过class Foo {}创建实例foo）：

```js
// 空类定义，有效
class Foo {}

// 有构造函数的类，有效
class Bar {
  constructor() {}
}

// 有获取函数的类，有效
class Baz {
  get myBaz() {}
}

// 有静态方法的类，有效
class Qux {
  static myQux() {}
}
```

类表达式的名称是可选的。在把类表达式赋值给变量后，可以通过name属性取得类表达式的名称字符串。但不能在类表达式作用域外部访问这个标识符。

```js
let Person = class PersonName {
  identify() {
    console.log(Person.name, PersonName.name);
  }
}

let p = new Person();

p.identify();               // PersonName PersonName

console.log(Person.name);   // PersonName
console.log(PersonName);    // ReferenceError: PersonName is not defined
```

### 类构造函数

`constructor`关键字用于在类定义块内部创建类的构造函数。方法名`constructor`会告诉解释器在使用`new`操作符创建类的新实例时，应该调用这个函数。构造函数的定义不是必需的，不定义构造函数相当于将构造函数定义为空函数。

#### 实例化

使用`new`操作符实例化`Person`的操作等于使用`new`调用其构造函数。唯一可感知的不同之处就是，JavaScript解释器知道使用`new`和类意味着应该使用`constructor`函数进行实例化。

使用new调用类的构造函数会执行如下操作。

**(1) 在内存中创建一个新对象。**

**(2) 这个新对象内部的[[Prototype]]指针被赋值为构造函数的prototype属性。**

**(3) 构造函数内部的this被赋值为这个新对象（即this指向新对象）。**

**(4) 执行构造函数内部的代码（给新对象添加属性）。**

**(5) 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。**

```js
class Animal {}

class Person {
  constructor() {
    console.log('person ctor');
  }
}

class Vegetable {
  constructor() {
    this.color = 'orange';
  }
}

let a = new Animal();

let p = new Person();  // person ctor

let v = new Vegetable();
console.log(v.color);  // orange
```

类实例化时传入的参数会用作构造函数的参数。如果不需要参数，则类名后面的括号也是可选的：

```js
class Person {
  constructor(name) {
    console.log(arguments.length);
    this.name = name || null;
  }
}

let p1 = new Person;          // 0
console.log(p1.name);         // null

let p2 = new Person();        // 0
console.log(p2.name);         // null

let p3 = new Person('Jake');  // 1
console.log(p3.name);         // Jake
```

默认情况下，类构造函数会在执行之后返回`this`对象。构造函数返回的对象会被用作实例化的对象，如果没有什么引用新创建的`this`对象，那么这个对象会被销毁。不过，如果返回的不是this对象，而是其他对象，那么这个对象不会通过instanceof操作符检测出跟类有关联，因为这个对象的原型指针并没有被修改。

```js
class Person {
  constructor(override) {
    this.foo = 'foo';
    if (override) {
      return {
        bar: 'bar'
      };
    }
  }
}

let p1 = new Person(),
    p2 = new Person(true);

console.log(p1);                    // Person{ foo: 'foo' }
console.log(p1 instanceof Person);  // true

console.log(p2);                    // { bar: 'bar' }
console.log(p2 instanceof Person);  // false
```

类构造函数与构造函数的主要区别是，调用类构造函数必须使用new操作符。而普通构造函数如果不使用new调用，那么就会以全局的this（通常是window）作为内部对象。调用类构造函数时如果忘了使用new则会抛出错误：

```js
function Person() {}

class Animal {}

// 把window作为this来构建实例
let p = Person();

let a = Animal();
// TypeError: class constructor Animal cannot be invoked without 'new'
```

类构造函数没有什么特殊之处，实例化之后，它会成为普通的实例方法（但作为类构造函数，仍然要使用new调用）。因此，实例化之后可以在实例上引用它：

```js
class Person {}

// 使用类创建一个新实例
let p1 = new Person();

p1.constructor();
// TypeError: Class constructor Person cannot be invoked without 'new'

// 使用对类构造函数的引用创建一个新实例
let p2 = new p1.constructor();
```

#### 把类当成特殊函数

ECMAScript中没有正式的类这个类型。从各方面来看，ECMAScript类就是一种特殊函数。
```js
class Person {}

console.log(Person);         // class Person {}
console.log(typeof Person);  // function

console.log(Person.prototype);                         // { constructor: f() }
console.log(Person === Person.prototype.constructor);  // true

let p = new Person();
console.log(p instanceof Person); // true  //此时的类构造函数要使用类标识符
```

类本身具有与普通构造函数一样的行为。在类的上下文中，类本身在使用new调用时就会被当成构造函数。重点在于，类中定义的constructor方法**不会**被当成构造函数，在对它使用instanceof操作符时会返回false。但是，如果在创建实例时直接将类构造函数当成普通构造函数来使用，那么instanceof操作符的返回值会反转：

```js
class Person {}

let p1 = new Person();

console.log(p1.constructor === Person);         // true
console.log(p1 instanceof Person);              // true
console.log(p1 instanceof Person.constructor);  // false

let p2 = new Person.constructor();

console.log(p2.constructor === Person);         // false
console.log(p2 instanceof Person);              // false
console.log(p2 instanceof Person.constructor);  // true

function name() {}
let n1 = new name();

console.log(n1 instanceof name.constructor)     // false
```

类是JavaScript的一等公民，因此可以像其他对象或函数引用一样把类作为参数传递：
```js
// 类可以像函数一样在任何地方定义，比如在数组中
let classList = [
  class {
    constructor(id) {
      this.id_ = id;
      console.log(`instance ${this.id_}`);
    }
  }
];

function createInstance(classDefinition, id) {
  return new classDefinition(id);
}

let foo = createInstance(classList[0], 3141);  // instance 3141
```

与立即调用函数表达式相似，类也可以立即实例化：

```js
// 因为是一个类表达式，所以类名是可选的
let p = new class Foo {
  constructor(x) {
    console.log(x);
  }
}('bar');        // bar

console.log(p);  // Foo {}
```

### 实例、原型和类成员

类的语法可以非常方便地定义应该存在于实例上的成员、应该存在于原型上的成员，以及应该存在于类本身的成员。

#### 实例成员

每次通过`new`调用类标识符时，都会执行类构造函数。在这个函数内部，可以为新创建的实例（`this`）添加“自有”属性。至于添加什么样的属性，则没有限制。另外，在构造函数执行完毕后，仍然可以给实例继续添加新成员。

每个实例都对应一个唯一的成员对象，这意味着所有成员都不会在原型上共享：

```js
class Person {
  constructor() {
    // 这个例子先使用对象包装类型定义一个字符串
    // 为的是在下面测试两个对象的相等性
    this.name = new String('Jack');

    this.sayName = () => console.log(this.name);

    this.nicknames = ['Jake', 'J-Dog']
  }
}

let p1 = new Person(),
    p2 = new Person();

p1.sayName(); // Jack
p2.sayName(); // Jack

console.log(p1.name === p2.name);            // false
console.log(p1.sayName === p2.sayName);      // false
console.log(p1.nicknames === p2.nicknames);  // false

p1.name = p1.nicknames[0];
p2.name = p2.nicknames[1];

p1.sayName();  // Jake
p2.sayName();  // J-Dog
```

#### 原型方法与访问器

```js
class Person {
  constructor() {
    // 添加到this的所有内容都会存在于不同的实例上
    this.locate = () => console.log('instance');
  }

  // 在类块中定义的所有内容都会定义在类的原型上
  locate() {
    console.log('prototype');
  }
}

let p = new Person();

p.locate();                 // instance
Person.prototype.locate();  // prototype
```

可以把方法定义在类构造函数中或者类块中，但不能在类块中给原型添加原始值或对象作为成员数据：

```js
class Person {
  name: 'Jake'
}
// Uncaught SyntaxError: Unexpected token
```

类方法等同于对象属性，因此可以使用字符串、符号或计算的值作为键：

```js
const symbolKey = Symbol('symbolKey');

class Person {

  stringKey() {
    console.log('invoked stringKey');
  }
   [symbolKey]() {
    console.log('invoked symbolKey');
  }
   ['computed' + 'Key']() {
    console.log('invoked computedKey');
  }
}

let p = new Person();

p.stringKey();    // invoked stringKey
p[symbolKey]();   // invoked symbolKey
p.computedKey();  // invoked computedKey
```

类定义也支持获取和设置访问器。语法与行为跟普通对象一样：  

```js
class Person {
  set name(newName) {
    this.name_ = newName;
  }

  get name() {
    return this.name_;
  }
}

let p = new Person();
p.name = 'Jake';
console.log(p.name); // Jake
```

#### 静态类方法

可以在类上定义静态方法。这些方法通常用于执行不特定于实例的操作，也不要求存在类的实例。与原型成员类似，静态成员每个类上只能有一个。

静态类成员在类定义中使用`static`关键字作为前缀。在静态成员中，`this`引用类自身。其他所有约定跟原型成员一样：

```js
class Person {
  constructor() {
    // 添加到this的所有内容都会存在于不同的实例上
    this.locate = () => console.log('instance', this);
  }

  // 定义在类的原型对象上
  locate() {
    console.log('prototype', this);
  }

  // 定义在类本身上
  static locate() {
    console.log('class', this);
  }
}

let p = new Person();

p.locate();                 // instance, Person {}
Person.prototype.locate();  // prototype, {constructor: ... }
Person.locate();            // class, class Person {}
```

静态类方法非常适合作为实例工厂：

```js
class Person {
  constructor(age) {
    this.age_ = age;
  }

  sayAge() {
    console.log(this.age_);
  }

  static create() {
    // 使用随机年龄创建并返回一个Person实例
    return new Person(Math.floor(Math.random()*100));
  }
}

console.log(Person.create()); // Person { age_: ... }
```

#### 非函数原型和类成员

虽然类定义并不显式支持在原型或类上添加成员数据，但在类定义外部，可以手动添加.

```js
class Person {
  sayName() {
    console.log(`${Person.greeting} ${this.name}`);
  }
}

// 在类上定义数据成员
Person.greeting = 'My name is';

// 在原型上定义数据成员
Person.prototype.name = 'Jake';

let p = new Person();
p.sayName();  // My name is Jake
```

!> 注意　类定义中之所以没有显式支持添加数据成员，是因为在共享目标（原型和类）上添加可变（可修改）数据成员是一种反模式。一般来说，对象实例应该独自拥有通过`this`引用的数据。

#### 迭代器与生成器方法

类定义语法支持在原型和类本身上定义生成器方法：

```js
class Person {
  // 在原型上定义生成器方法
  *createNicknameIterator() {
    yield 'Jack';
    yield 'Jake';
    yield 'J-Dog';
  }

  // 在类上定义生成器方法
  static *createJobIterator() {
    yield 'Butcher';
    yield 'Baker';
    yield 'Candlestick maker';
  }
}

let jobIter = Person.createJobIterator();
console.log(jobIter.next().value);  // Butcher
console.log(jobIter.next().value);  // Baker
console.log(jobIter.next().value);  // Candlestick maker

let p = new Person();
let nicknameIter = p.createNicknameIterator();
console.log(nicknameIter.next().value);  // Jack
console.log(nicknameIter.next().value);  // Jake
console.log(nicknameIter.next().value);  // J-Dog
```

因为支持生成器方法，所以可以通过添加一个默认的迭代器，把类实例变成可迭代对象：

```js
class Person {
  constructor() {
    this.nicknames = ['Jack', 'Jake', 'J-Dog'];
  }

  *[Symbol.iterator]() {
    yield *this.nicknames.entries();
  }
}

let p = new Person();
for (let [idx, nickname] of p) {
  console.log(nickname);
}
// Jack
// Jake
// J-Dog
```

```js
class Person {
  constructor() {
    this.nicknames = ['Jack', 'Jake', 'J-Dog'];
  }

  [Symbol.iterator]() {
    return this.nicknames.entries();
  }
}

let p = new Person();
for (let [idx, nickname] of p) {
  console.log(nickname);
}
// Jack
// Jake
// J-Dog
```

### 继承

ECMAScript 6新增特性中最出色的一个就是原生支持了类继承机制。虽然类继承使用的是新语法，但背后依旧使用的是原型链。

#### 继承基础

ES6类支持单继承。使用`extends`关键字，就可以继承任何拥有`[[Construct]]`和原型的对象。很大程度上，这意味着不仅可以继承一个类，也可以继承普通的构造函数（保持向后兼容）：

```js
class Vehicle {}

// 继承类
class Bus extends Vehicle {}

let b = new Bus();
console.log(b instanceof Bus);      // true
console.log(b instanceof Vehicle);  // true
　
　
function Person() {}

// 继承普通构造函数
class Engineer extends Person {}

let e = new Engineer();
console.log(e instanceof Engineer);  // true
console.log(e instanceof Person);    // true
```

派生类都会通过原型链访问到类和原型上定义的方法。this的值会反映调用相应方法的实例或者类：

```js
class Vehicle {
  identifyPrototype(id) {
    console.log(id, this);
  }

  static identifyClass(id) {
    console.log(id, this);
  }
}

class Bus extends Vehicle {}

let v = new Vehicle();
let b = new Bus();

b.identifyPrototype('bus');       // bus, Bus {}
v.identifyPrototype('vehicle');   // vehicle, Vehicle {}

Bus.identifyClass('bus');         // bus, class Bus {}
Vehicle.identifyClass('vehicle'); // vehicle, class Vehicle {}
```

!> 注意　`extends`关键字也可以在类表达式中使用，因此`let Bar = class extends Foo {}`是有效的语法。

#### 构造函数、HomeObject和super()

派生类的方法可以通过`super`关键字引用它们的原型。这个关键字只能在派生类中使用，而且仅限于类构造函数、实例方法和静态方法内部。在类构造函数中使用`super`可以调用父类构造函数。

```js
class Vehicle {
  constructor() {
    this.hasEngine = true;
  }
}

class Bus extends Vehicle {
  constructor() {
    // 不要在调用super()之前引用this，否则会抛出ReferenceError

    super(); // 相当于super.constructor()

    console.log(this instanceof Vehicle);  // true
    console.log(this);                     // Bus { hasEngine: true }
  }
}

new Bus();
```

在静态方法中可以通过super调用继承的类上定义的静态方法：

```js
class Vehicle {
  static identify() {
    console.log('vehicle');
  }
}

class Bus extends Vehicle {
  static identify() {
    super.identify();
  }
}

Bus.identify();  // vehicle
```
!> 注意　ES6给类构造函数和静态方法添加了内部特性`[[HomeObject]]`，这个特性是一个指针，指向定义该方法的对象。这个指针是自动赋值的，而且只能在JavaScript引擎内部访问。`super`始终会定义为`[[HomeObject]]`的原型。

**在使用super时要注意几个问题。**

+ `super`只能在派生类构造函数和静态方法中使用。

```js
class Vehicle {
  constructor() {
    super();
    // SyntaxError: 'super' keyword unexpected
  }
}
```

+ 不能单独引用`super`关键字，要么用它调用构造函数，要么用它引用静态方法。

```js
class Vehicle {}

class Bus extends Vehicle {
  constructor() {
    console.log(super);
    // SyntaxError: 'super' keyword unexpected here
  }
}
```

+ 调用`super()`会调用父类构造函数，并将返回的实例赋值给this。

```js
class Vehicle {}

class Bus extends Vehicle {
  constructor() {
    super();

    console.log(this instanceof Vehicle);
  }
}

new Bus(); // true
```

+ `super()`的行为如同调用构造函数，如果需要给父类构造函数传参，则需要手动传入。

```js
class Vehicle {
  constructor(licensePlate) {
    this.licensePlate = licensePlate;
  }
}

class Bus extends Vehicle {
  constructor(licensePlate) {
    super(licensePlate);
  }
}

console.log(new Bus('1337H4X')); // Bus { licensePlate: '1337H4X' }
```


+ 如果没有定义类构造函数，在实例化派生类时会调用super()，而且会传入所有传给派生类的参数。

```js
class Vehicle {
  constructor(licensePlate) {
    this.licensePlate = licensePlate;
  }
}

class Bus extends Vehicle {}

console.log(new Bus('1337H4X')); // Bus { licensePlate: '1337H4X' }
```

+ 在类构造函数中，不能在调用super()之前引用this。

```js
class Vehicle {}

class Bus extends Vehicle {
  constructor() {
    console.log(this);
  }
}

new Bus();
// ReferenceError: Must call super constructor in derived class
// before accessing 'this' or returning from derived constructor
```

+ **如果在派生类中显式定义了构造函数，则要么必须在其中调用super()，要么必须在其中返回一个对象**。

```js
class Vehicle {}

class Car extends Vehicle {}

class Bus extends Vehicle {
  constructor() {
    super();
  }
}

class Van extends Vehicle {
  constructor() {
    return {};
  }
}

console.log(new Car());  // Car {}
console.log(new Bus());  // Bus {}
console.log(new Van());  // {}
```

#### 抽象基类

有时候可能需要定义这样一个类，它可供其他类继承，但本身不会被实例化。虽然ECMAScript没有专门支持这种类的语法 ，但通过`new.target`也很容易实现。`new.target`保存通过`new`关键字调用的类或函数。通过在实例化时检测`new.target`是不是抽象基类，可以阻止对抽象基类的实例化：

```js
// 抽象基类
class Vehicle {
  constructor() {
    console.log(new.target);
    if (new.target === Vehicle) {
      throw new Error('Vehicle cannot be directly instantiated');
    }
  }
}

// 派生类
class Bus extends Vehicle {}

new Bus();       // class Bus {}
new Vehicle();   // class Vehicle {}
// Error: Vehicle cannot be directly instantiated
```

另外，通过在抽象基类构造函数中进行检查，可以要求派生类必须定义某个方法。因为原型方法在调用类构造函数之前就已经存在了，所以可以通过`this`关键字来检查相应的方法：

```js
// 抽象基类
class Vehicle {
  constructor() {
    if (new.target === Vehicle) {
      throw new Error('Vehicle cannot be directly instantiated');
    }

    if (!this.foo) {
      throw new Error('Inheriting class must define foo()');
    }

    console.log('success!');
  }
}

// 派生类
class Bus extends Vehicle {
  foo() {}
}

// 派生类
class Van extends Vehicle {}

new Bus(); // success!
new Van(); // Error: Inheriting class must define foo()
```

#### 继承内置类型

```js
class SuperArray extends Array {
  shuffle() {
    // 洗牌算法
    for (let i = this.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [this[i], this[j]] = [this[j], this[i]];
    }
  }
}

let a = new SuperArray(1, 2, 3, 4, 5);

console.log(a instanceof Array);       // true
console.log(a instanceof SuperArray);  // true

console.log(a);  // [1, 2, 3, 4, 5]
a.shuffle();
console.log(a);  // [3, 1, 4, 5, 2]
```

有些内置类型的方法会返回新实例。默认情况下，返回实例的类型与原始实例的类型是一致的：

```js
class SuperArray extends Array {}

let a1 = new SuperArray(1, 2, 3, 4, 5);
let a2 = a1.filter(x => !!(x%2))

console.log(a1);  // [1, 2, 3, 4, 5]
console.log(a2);  // [1, 3, 5]
console.log(a1 instanceof SuperArray);  // true
console.log(a2 instanceof SuperArray);  // true
```

如果想覆盖这个默认行为，则可以覆盖`Symbol.species`访问器，这个访问器决定在创建返回的实例时使用的类：

```js
class SuperArray extends Array {
  static get [Symbol.species]() {
    return Array;
  }
}

let a1 = new SuperArray(1, 2, 3, 4, 5);
let a2 = a1.filter(x => !!(x%2))

console.log(a1);  // [1, 2, 3, 4, 5]
console.log(a2);  // [1, 3, 5]
console.log(a1 instanceof SuperArray);  // true
console.log(a2 instanceof SuperArray);  // false
```

#### **类混入**

把不同类的行为集中到一个类是一种常见的JavaScript模式。虽然ES6没有显式支持多类继承，但通过现有特性可以轻松地模拟这种行为。

!> 注意　`Object.assign()`方法是为了混入对象行为而设计的。只有在需要混入类的行为时才有必要自己实现混入表达式。如果只是需要混入多个对象的属性，那么使用`Object.assign()`就可以了。

在下面的代码片段中，`extends`关键字后面是一个JavaScript表达式。任何可以解析为一个类或一个构造函数的表达式都是有效的。这个表达式会在求值类定义时被求值：

```js
class Vehicle {}

function getParentClass() {
  console.log('evaluated expression');
  return Vehicle;
}

class Bus extends getParentClass() {}
// 可求值的表达式
```

混入模式可以通过在一个表达式中连缀多个混入元素来实现，这个表达式最终会解析为一个可以被继承的类。

一个策略是定义一组“可嵌套”的函数，每个函数分别接收一个超类作为参数，而将混入类定义为这个参数的子类，并返回这个类。这些组合函数可以连缀调用，最终组合成超类表达式：

```js
class Vehicle {}

let FooMixin = (Superclass) => class extends Superclass {
  foo() {
    console.log('foo');
  }
};
let BarMixin = (Superclass) => class extends Superclass {
  bar() {
    console.log('bar');
  }
};
let BazMixin = (Superclass) => class extends Superclass {
  baz() {
    console.log('baz');
  }
};

class Bus extends FooMixin(BarMixin(BazMixin(Vehicle))) {}

let b = new Bus();
b.foo();  // foo
b.bar();  // bar
b.baz();  // baz
```

通过写一个辅助函数，可以把嵌套调用展开：

```js
class Vehicle {}

let FooMixin = (Superclass) => class extends Superclass {
  foo() {
    console.log('foo');
  }
};
let BarMixin = (Superclass) => class extends Superclass {
  bar() {
    console.log('bar');
  }
};
let BazMixin = (Superclass) => class extends Superclass {
  baz() {
    console.log('baz');
  }
};

function mix(BaseClass, ...Mixins) {
  return Mixins.reduce((accumulator, current) => current(accumulator), BaseClass);
}

class Bus extends mix(Vehicle, FooMixin, BarMixin, BazMixin) {}

let b = new Bus();
b.foo();  // foo
b.bar();  // bar
b.baz();  // baz
```

!> 注意　很多JavaScript框架（特别是React）已经抛弃混入模式，转向了组合模式（把方法提取到独立的类和辅助对象中，然后把它们组合起来，但不使用继承）。这反映了那个众所周知的软件设计原则：“组合胜过继承（composition over inheritance）。”这个设计原则被很多人遵循，在代码设计中能提供极大的灵活性。

## 总结

**创建对象**

+ 工厂模式就是一个简单的函数，这个函数可以创建对象，为它添加属性和方法，然后返回这个对象。这个模式在构造函数模式出现后就很少用了。
使用构造函数模式可以自定义引用类型，可以使用new关键字像创建内置类型实例一样创建自定义类型的实例。
  - 不过，构造函数模式也有不足，主要是其成员无法重用，包括函数。考虑到函数本身是松散的、弱类型的，没有理由让函数不能在多个对象实例间共享。

+ 原型模式解决了成员共享的问题，只要是添加到构造函数prototype上的属性和方法就可以共享。而组合构造函数和原型模式通过构造函数定义实例属性，通过原型定义共享的属性和方法。
JavaScript的继承主要通过原型链来实现。原型链涉及把构造函数的原型赋值为另一个类型的实例。这样一来，子类就可以访问父类的所有属性和方法，就像基于类的继承那样。
  - 原型链的问题是所有继承的属性和方法都会在对象实例间共享，无法做到实例私有。

+ 盗用构造函数模式通过在子类构造函数中调用父类构造函数，可以避免这个问题。这样可以让每个实例继承的属性都是私有的，但要求类型只能通过构造函数模式来定义（因为子类不能访问父类原型上的方法）。

+ 目前最流行的继承模式是组合继承，即通过原型链继承共享的属性和方法，通过盗用构造函数继承实例属性。

**继承模式**

+ 原型式继承可以无须明确定义构造函数而实现继承，本质上是对给定对象执行浅复制。这种操作的结果之后还可以再进一步增强。

+ 与原型式继承紧密相关的是寄生式继承，即先基于一个对象创建一个新对象，然后再增强这个新对象，最后返回新对象。这个模式也被用在组合继承中，用于避免重复调用父类构造函数导致的浪费。

+ 寄生组合继承被认为是实现基于类型继承的最有效方式。

+ ECMAScript 6新增的类很大程度上是基于既有原型机制的语法糖。类的语法让开发者可以优雅地定义向后兼容的类，既可以继承内置类型，也可以继承自定义类型。类有效地跨越了对象实例、对象原型和对象类之间的鸿沟。