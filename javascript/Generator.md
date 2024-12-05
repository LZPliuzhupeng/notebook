## 生成器

生成器是ECMAScript 6新增的一个极为灵活的结构，拥有在一个函数块内暂停和恢复代码执行的能力。

生成器的形式是一个函数，函数名称前面加一个星号（`*`）表示它是一个生成器。只要是可以定义函数的地方，就可以定义生成器。
```js
// 生成器函数声明
function* generatorFn() {}

// 生成器函数表达式
let generatorFn = function* () {}

// 作为对象字面量方法的生成器函数
let foo = {
  * generatorFn() {}
}

// 作为类实例方法的生成器函数
class Foo {
  * generatorFn() {}
}

// 作为类静态方法的生成器函数
class Bar {
  static * generatorFn() {}
} 
```

!> 注意:箭头函数不能用来定义生成器函数。

#### 标识生成器函数的星号不受两侧空格的影响：

```js
// 等价的生成器函数：
function* generatorFnA() {}
function *generatorFnB() {}
function * generatorFnC() {}

// 等价的生成器方法：
class Foo {
  *generatorFnD() {}
  * generatorFnE() {}
}
```

## next()

调用生成器函数会产生一个生成器对象。

生成器对象一开始处于暂停执行（`suspended`）的状态。与迭代器相似，生成器对象也实现了`Iterator`接口，因此具有`next()`方法。调用这个方法会让生成器开始或恢复执行。

```js
function* generatorFn() {}

const g = generatorFn();

console.log(g);       // generatorFn {<suspended>}
console.log(g.next);  // f next() { [native code] }
```

`next()`方法的返回值类似于迭代器，有一个`done`属性和一个`value`属性。函数体为空的生成器函数中间不会停留，调用一次`next()`就会让生成器到达`done: true`状态。

```js
function* generatorFn() {}

let generatorObject = generatorFn();

console.log(generatorObject);         // generatorFn {<suspended>}
console.log(generatorObject.next());  // { done: true, value: undefined }
console.log(generatorObject.next());  // { done: true, value: undefined }
```

`value`属性是生成器函数的返回值，默认值为`undefined`，可以通过生成器函数的返回值指定：

```js
function* generatorFn() {
  return 'foo';
}

let generatorObject = generatorFn();

console.log(generatorObject);         // generatorFn {<suspended>}
console.log(generatorObject.next());  // { done: true, value: 'foo' }
```

```js
function* generatorFn() {
  console.log('foobar');
}

// 初次调用生成器函数并不会打印日志
let generatorObject = generatorFn();

generatorObject.next();  // foobar
```

## yield

`yield`关键字可以让生成器停止和开始执行，也是生成器最有用的地方。

生成器函数在遇到`yield`关键字之前会正常执行。遇到这个关键字后，执行会停止，函数作用域的状态会被保留。

停止执行的生成器函数只能通过在生成器对象上调用`next()`方法来恢复执行：

```js
function* generatorFn() {
  yield;
}

let generatorObject = generatorFn();

console.log(generatorObject.next());  // { done: false, value: undefined }
console.log(generatorObject.next());  // { done: true, value: undefined }
```

此时的`yield`关键字有点像函数的中间返回语句，它生成的值会出现在`next()`方法返回的对象里。

通过`yield`关键字退出的生成器函数会处在`done: false`状态；通过`return`关键字退出的生成器函数会处于`done: true`状态。

```js
function* generatorFn() {
  yield 'foo';
  yield 'bar';
  return 'baz';
}

let generatorObject = generatorFn();

console.log(generatorObject.next());  // { done: false, value: 'foo' }
console.log(generatorObject.next());  // { done: false, value: 'bar' }
console.log(generatorObject.next());  // { done: true, value: 'baz' }
```

生成器函数内部的执行流程会针对每个生成器对象区分作用域。在一个生成器对象上调用next()不会影响其他生成器：

```js
function* generatorFn() {
  yield 'foo';
  yield 'bar';
  return 'baz';
}

let generatorObject1 = generatorFn();
let generatorObject2 = generatorFn();


console.log(generatorObject1.next()); // { done: false, value: 'foo' }
console.log(generatorObject2.next()); // { done: false, value: 'foo' }
console.log(generatorObject2.next()); // { done: false, value: 'bar' }
console.log(generatorObject1.next()); // { done: false, value: 'bar' }
```

`yield`关键字只能在生成器函数内部使用，用在其他地方会抛出错误。类似函数的`return`关键字，`yield`关键字必须直接位于生成器函数定义中，出现在嵌套的非生成器函数中会抛出语法错误：

// 有效
function* validGeneratorFn() {
  yield;
}

// 无效
function* invalidGeneratorFnA() {
  function a() {
    yield;
  }
}

// 无效
function* invalidGeneratorFnB() {
  const b = () => {
    yield;
  }
}

// 无效
function* invalidGeneratorFnC() {
  (() => {
    yield;
  })();
}

#### 使用yield实现输入和输出

除了可以作为函数的中间返回语句使用，`yield`关键字还可以作为函数的中间参数使用。上一次让生成器函数暂停的`yield`关键字会接收到传给`next()`方法的第一个值。

!> 这里有个地方不太好理解——第一次调用`next()`传入的值不会被使用，因为这一次调用是为了开始执行生成器函数：

```js
function* generatorFn(initial) {
  console.log(initial);
  console.log(yield);
  console.log(yield);
}

let generatorObject = generatorFn('foo');

generatorObject.next('bar');  // foo
generatorObject.next('baz');  // baz
generatorObject.next('qux');  // qux
```

`yield`关键字可以同时用于输入和输出，如下例所示：

```js
function* generatorFn() {
  return yield 'foo';
}

let generatorObject = generatorFn();

console.log(generatorObject.next());       // { done: false, value: 'foo' }
console.log(generatorObject.next('bar'));  // { done: true, value: 'bar' }
```

#### 生成器作为默认迭代器

因为生成器对象实现了`Iterable`接口，而且生成器函数和默认迭代器被调用之后都产生迭代器，所以生成器格外适合作为默认迭代器。下面是一个简单的例子，这个类的默认迭代器可以用一行代码产出类的内容：

```js
class Foo {
  constructor() {
    this.values = [1, 2, 3];
  }
  * [Symbol.iterator]() {
    yield* this.values;
  }
}

const f = new Foo();
for (const x of f) {
  console.log(x);
}
// 1
// 2
// 3
```

## 提前终止生成器

与迭代器类似，生成器也支持“可关闭”的概念。一个实现`Iterator`接口的对象一定有`next()`方法，还有一个可选的`return()`方法用于提前终止迭代器。生成器对象除了有这两个方法，还有第三个方法：`throw()`。

```js
function* generatorFn() {}

const g = generatorFn();

console.log(g);         // generatorFn {<suspended>}
console.log(g.next);    // f next() { [native code] }
console.log(g.return);  // f return() { [native code] }
console.log(g.throw);   // f throw() { [native code] }
```

> `return()`和`throw()`方法都可以用于强制生成器进入关闭状态。

### return()

与迭代器不同，所有生成器对象都有`return()`方法，只要通过它进入关闭状态，就无法恢复了。后续调用`next()`会显示`done: true`状态，而提供的任何返回值都不会被存储或传播：

```js
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    yield x;
  }
}

const g = generatorFn();

console.log(g.next());     // { done: false, value: 1 }
console.log(g.return(4));  // { done: true, value: 4 }
console.log(g.next());     // { done: true, value: undefined }
console.log(g.next());     // { done: true, value: undefined }
console.log(g.next());     // { done: true, value: undefined }
```

### `(不懂)`for-of循环等内置语言结构会忽略状态为done: true的IteratorObject内部返回的值。

```js
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    yield x;
  }
}

const g = generatorFn();

for (const x of g) {
  if (x > 1) {
    g.return(4);
  }
  console.log(x);
}
// 1
// 2
```

### throw()

`throw()`方法会在暂停的时候将一个提供的错误注入到生成器对象中。如果错误未被处理，生成器就会关闭：

```js
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    yield x;
  }
}

const g = generatorFn();

console.log(g);   // generatorFn {<suspended>}
try {
  g.throw('foo');
} catch (e) {
  console.log(e); // foo
}
console.log(g);   // generatorFn {<closed>}
```

不过，假如生成器函数内部处理了这个错误，那么生成器就不会关闭，而且还可以恢复执行。错误处理会跳过对应的yield，因此在这个例子中会跳过一个值。比如：

```js
function* generatorFn() {
  for (const x of [1, 2, 3]) {
    try {
      yield x;
    } catch(e) {}
  }
}

const g = generatorFn();

console.log(g.next()); // { done: false, value: 1}
g.throw('foo');
console.log(g.next()); // { done: false, value: 3}
```

在这个例子中，生成器在`try/catch`块中的`yield`关键字处暂停执行。

在暂停期间，`throw()`方法向生成器对象内部注入了一个错误：字符串`"foo"`。这个错误会被`yield`关键字抛出。

因为错误是在生成器的`try/catch`块中抛出的，所以仍然在生成器内部被捕获。

可是，由于`yield`抛出了那个错误，生成器就不会再产出值2。此时，生成器函数继续执行，在下一次迭代再次遇到`yield`关键字时产出了值3。

!> 注意:如果生成器对象还没有开始执行，那么调用`throw()`抛出的错误不会在函数内部被捕获，因为这相当于在函数块外部抛出了错误。