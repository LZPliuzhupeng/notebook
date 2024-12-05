## js

### static 关键字

```js
class MyClass {
  static staticMethod() {
    console.log("This is a static method");
  }
  constructor() {
    this.locate = () => console.log("instance");
  }
  locate() {
    console.log("prototype");
  }
}
```

```js
function MyClass() {
  this.locate = () => console.log("instance");
}

MyClass.staticMethod = function () {
  console.log("This is a static method");
};
MyClass.prototype.locate = function () {
  console.log("prototype");
};
```

### git stash

暂存

### call、bind、apply 的使用

- call：call 方法会立即执行函数。greeting.call(this, 'John');

- bind: bind 方法返回一个绑定了 this 值和参数的新函数，而不会立即执行函数。 const boundGreeting = greeting.bind(this);

- apply: apply 方法接收的参数是一个数组或类数组对象，apply 方法会立即执行函数。greeting.apply(this, ['John']);

## react

### React hooks？

- `useState`：用于管理功能组件中的状态。
- `useEffect`：用于在功能组件中执行副作用，例如获取数据或订阅事件。
- `useContext`：用于访问功能组件内的 React 上下文的值。
- `useRef`：用于创建对跨渲染持续存在的元素或值的可变引用。
- `useCallback`：用于记忆函数以防止不必要的重新渲染。
- `useMemo`：用于记忆值，通过缓存昂贵的计算来提高性能。
- `useReducer`：用于通过 reducer 函数管理状态，类似于 Redux 的工作原理。
- `useLayoutEffect`：与 useEffect 类似，但效果在所有 DOM 突变后同步运行。

### 受控组件和非受控组件有什么区别？

受控组件和非受控组件之间的区别在于**它们如何管理和更新其状态**。

受控组件是状态由 React 控制的组件。组件接收其当前值并通过 props 更新它。当值改变时它也会触发回调函数。这意味着该组件不存储其自己的内部状态。相反，父组件管理该值并将其传递给受控组件。

### hooks 为什么不能放在条件判断里

React Hooks 的使用必须遵循以下两个规则：

1、Hooks 必须在 React 函数组件的顶层使用：这意味着不能将 Hooks 放在条件语句、循环语句、嵌套函数中或任何非函数组件的地方。Hooks 的调用必须在每次组件渲染时以相同的顺序执行，这样 React 才能正确地跟踪每个 Hook 的状态。
2、Hooks 的调用顺序必须保持稳定：这意味着在组件的每次渲染中，Hooks 的调用顺序必须保持不变。这样 React 才能正确地将每个 Hook 与其对应的状态进行关联。

如果将 Hooks 放在条件判断语句中，会导致 Hooks 的调用顺序发生变化。例如，在某个条件满足时才调用 useState，而在其他条件下不调用。这样会导致 Hooks 的调用顺序不稳定，React 无法正确地跟踪和管理每个 Hook 的状态，可能会导致组件渲染错误、状态不一致或其他不可预测的行为。

### 父子组件的通信方式？

父组件向子组件通信：父组件通过 props 向子组件传递需要的信息。

```js
const Child = (props) => {
  return <p>{props.name}</p>;
};
const Parent = () => {
  return <Child name="react"></Child>;
};
```

### 子组件向父组件通信：: props+回调的方式。

```js
const Child = (props) => {
  const cb = (msg) => {
    return () => {
      props.callback(msg);
    };
  };
  return <button onClick={cb("你好!")}>你好</button>;
};
class Parent extends Component {
  callback(msg) {
    console.log(msg);
  }
  render() {
    return <Child callback={this.callback.bind(this)}></Child>;
  }
}
```

### React setState 调用之后发生了什么？是同步还是异步？

（1）React 中 setState 后发生了什么

在代码中调用 setState 函数之后，React 会将传入的参数对象与组件当前的状态合并，然后触发调和过程(Reconciliation)。经过调和过程，React 会以相对高效的方式根据新的状态构建 React 元素树并且着手重新渲染整个 UI 界面。
在 React 得到元素树之后，React 会自动计算出新的树与老树的节点差异，然后根据差异对界面进行最小化重渲染。在差异计算算法中，React 能够相对精确地知道哪些位置发生了改变以及应该如何改变，这就保证了按需更新，而不是全部重新渲染。
如果在短时间内频繁 setState。React 会将 state 的改变压入栈中，在合适的时机，批量更新 state 和视图，达到提高性能的效果。

（2）setState 是同步还是异步的

假如所有 setState 是同步的，意味着每执行一次 setState 时（有可能一个同步代码中，多次 setState），都重新 vnode diff + dom 修改，这对性能来说是极为不好的。如果是异步，则可以把一个同步代码中的多个 setState 合并成一次组件更新。所以默认是异步的，但是在一些情况下是同步的。
setState 并不是单纯同步/异步的，它的表现会因调用场景的不同而不同。在源码中，通过 isBatchingUpdates 来判断 setState 是先存进 state 队列还是直接更新，如果值为 true 则执行异步操作，为 false 则直接更新。

- 异步： 在 React 可以控制的地方，就为 true，比如在 React 生命周期事件和合成事件中，都会走合并操作，延迟更新的策略。
- 同步： 在 React 无法控制的地方，比如原生事件，具体就是在 addEventListener 、setTimeout、setInterval 等事件中，就只能同步更新。

一般认为，做异步设计是为了性能优化、减少渲染次数：

setState 设计为异步，可以显著的提升性能。如果每次调用 setState 都进行一次更新，那么意味着 render 函数会被频繁调用，界面重新渲染，这样效率是很低的；最好的办法应该是获取到多个更新，之后进行批量更新；
如果同步更新了 state，但是还没有执行 render 函数，那么 state 和 props 不能保持同步。state 和 props 不能保持一致性，会在开发中产生很多的问题；

### componentWillReceiveProps 的理解

该方法当 `props` 发生变化时执行，初始化 `render` 时不执行，在这个回调函数里面，你可以根据属性的变化，通过调用 `this.setState()`来更新你的组件状态，旧的属性还是可以通过 `this.props` 来获取,这里调用更新状态是安全的，并不会触发额外的 `render` 调用。

使用好处： 在这个生命周期中，可以在子组件的 render 函数执行前获取新的 props，从而更新子组件自己的 state。 可以将数据请求放在这里进行执行，需要传的参数则从 componentWillReceiveProps(nextProps)中获取。而不必将所有的请求都放在父组件中。于是该请求只会在该组件渲染时才会发出，从而减轻请求负担。

componentWillReceiveProps 在初始化 render 的时候不会执行，它会在 Component 接受到新的状态(Props)时被触发，一般用于父组件状态更新时子组件的重新渲染。

### React diff 算法的原理是什么？

实际上，diff 算法探讨的就是虚拟 DOM 树发生变化后，生成 DOM 树更新补丁的方式。它通过对比新旧两株虚拟 DOM 树的变更差异，将更新补丁作用于真实 DOM，以最小成本完成视图更新。

diff 算法可以总结为三个策略，分别从树、组件及元素三个层面进行复杂度的优化：

- 策略一：忽略节点跨层级操作场景，提升比对效率。（基于树进行对比）
  这一策略需要进行树比对，即对树进行分层比较。树比对的处理手法是非常“暴力”的，即两棵树只对同一层次的节点进行比较，如果发现节点已经不存在了，则该节点及其子节点会被完全删除掉，不会用于进一步的比较，这就提升了比对效率。

## next - router

## vue3

## 谷歌插件

## 微信小程序
