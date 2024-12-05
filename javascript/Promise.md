# 期约与异步函数

使用异步日志输出的方式`setTimeout(console.log, 0, .. params)`，旨在演示执行顺序及其他异步行为。

## 异步编程

略。。。

### 同步与异步

**同步行为**对应内存中顺序执行的处理器指令。

**异步行为**类似于系统中断，即当前进程外部的实体可以触发代码执行。

### 以往的异步编程模式

在早期的JavaScript中，只支持定义回调函数来表明异步操作完成。串联多个异步操作是一个常见的问题，通常需要深度嵌套的回调函数（俗称“回调地狱”）来解决。
```js
function double(value) {
  setTimeout(
    () => setTimeout(console.log, 0, value * 2)
  , 1000);
}

double(3);
// 6（大约1000毫秒之后）
```

`setTimeout`可以定义一个在指定时间之后会被调度执行的回调函数。对这个例子而言，1000毫秒之后，JavaScript运行时会把回调函数推到自己的消息队列上去等待执行。推到队列之后，回调什么时候出列被执行对JavaScript代码就完全不可见了。还有一点，`double()`函数在`setTimeout`成功调度异步操作之后会立即退出。

#### 异步返回值

一个策略是给异步操作提供一个回调，这个回调中包含要使用异步返回值的代码（作为回调的参数）。
```js
function double(value, callback) {
  setTimeout(() => callback(value * 2), 1000);
}

double(3, (x) => console.log(`I was given: ${x}`));
// I was given: 6（大约1000毫秒之后）
```

#### 失败处理

```js
function double(value, success, failure) {
  setTimeout(() => {
    try {
      if (typeof value !== 'number') {
        throw 'Must provide number as first argument';
      }
      success(2 * value);
    } catch (e) {
      failure(e);
    }
  }, 1000);
}

const successCallback = (x) => console.log(`Success: ${x}`);
const failureCallback = (e) => console.log(`Failure: ${e}`);

double(3, successCallback, failureCallback);
double('b', successCallback, failureCallback);

// Success: 6（大约1000毫秒之后）
// Failure: Must provide number as first argument（大约1000毫秒之后）
```

这种模式已经不可取了，因为必须在初始化异步操作时定义回调。异步函数的返回值只在短时间内存在，只有预备好将这个短时间内存在的值作为参数的回调才能接收到它。

#### 嵌套异步回调

如果异步返值又依赖另一个异步返回值，那么回调的情况还会进一步变复杂。
```js
function double(value, success, failure) {
  setTimeout(() => {
    try {
      if (typeof value !== 'number') {
        throw 'Must provide number as first argument';
      }
      success(2 * value);
    } catch (e) {
      failure(e);
    }
  }, 1000);
}

const successCallback = (x) => {
  double(x, (y) => console.log(`Success: ${y}`));
};
const failureCallback = (e) => console.log(`Failure: ${e}`);

double(3, successCallback, failureCallback);

// Success: 12（大约1000毫秒之后）
```

显然，随着代码越来越复杂，回调策略是不具有扩展性的。“回调地狱”这个称呼可谓名至实归。嵌套回调的代码维护起来就是噩梦。

## 期约(Promise)

期约是对尚不存在结果的一个替身。期约（promise）这个名字最早是由Daniel Friedman和David Wise在他们于1976年发表的论文“The Impact of Applicative Programming on Multiprocessing”中提出来的。但直到十几年以后，Barbara Liskov和Liuba Shrira在1988年发表了论文“Promises—Linguistic Support for Efficient Asynchronous Procedure Calls in Distributed Systems”，这个概念才真正确立下来。同一时期的计算机科学家还使用了“终局”（eventual）、“期许”（future）、“延迟”（delay）和“迟付”（deferred）等术语指代同样的概念。所有这些概念描述的都是一种异步程序执行的机制。

### Promises/A+规范

早期的期约机制在jQuery和Dojo中是以Deferred API的形式出现的。到了2010年，CommonJS项目实现的Promises/A规范日益流行起来。Q和Bluebird等第三方JavaScript期约库也越来越得到社区认可，虽然这些库的实现多少都有些不同。为弥合现有实现之间的差异，2012年Promises/A+组织分叉（fork）了CommonJS的Promises/A建议，并以相同的名字制定了Promises/A+规范。这个规范最终成为了ECMAScript 6规范实现的范本。

ECMAScript 6增加了对Promises/A+规范的完善支持，即`Promise`类型。一经推出，`Promise`就大受欢迎，成为了主导性的异步编程机制。所有现代浏览器都支持ES6期约，很多其他浏览器API（如`fetch()`和Battery Status API）也以期约为基础。