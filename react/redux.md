#### redux

#### 顶级暴露的方法 

+ createStore(reducer, [preloadedState], [enhancer])
+ combineReducers(reducers)
+ applyMiddleware(...middlewares)
+ bindActionCreators(actionCreators, dispatch)
+ compose(...functions)

#### Store API

+ Store
  - getState()
  - dispatch(action)
  - subscribe(listener)
  - getReducer()
  - replaceReducer(nextReducer)

#### 引入
上面介绍的所有函数都是顶级暴露的方法。都可以这样引入：

##### ES6
```js
import { createStore } from 'redux';
```

##### ES5 (CommonJS)
```js
var createStore = require('redux').createStore;
```

##### ES5 (UMD build)
```js
var createStore = Redux.createStore;
```


## createStore

`createStore(reducer, [preloadedState], enhancer)`

创建一个 Redux [store](/react/redux?id=store) 来以存放应用中所有的 state。
应用中应有且仅有一个 store。

**参数**

+ `reducer` *(Function)*: 接收两个参数，分别是当前的 state 树和要处理的 action，返回新的 state 树。
+ `[preloadedState]` *(any)*: 初始时的 state。 在同构应用中，你可以决定是否把服务端传来的 state 水合（hydrate）后传给它，或者从之前保存的用户会话中恢复一个传给它。如果你使用 combineReducers 创建 reducer，它必须是一个普通对象，与传入的 keys 保持同样的结构。否则，你可以自由传入任何 reducer 可理解的内容。
+ `enhancer` *(Function)*: Store enhancer 是一个组合 store creator 的高阶函数，返回一个新的强化过的 store creator。这与 middleware 相似，它也允许你通过复合函数改变 store 接口。


**返回值**

(`Store`): 保存了应用所有 state 的对象。改变 state 的惟一方法是 dispatch action。你也可以 subscribe 监听 state 的变化，然后更新 UI。

```js
import { createStore } from 'redux'

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([action.text])
    default:
      return state
  }
}

let store = createStore(todos, ['Use Redux'])

store.dispatch({
  type: 'ADD_TODO',
  text: 'Read the docs'
})

console.log(store.getState())
// [ 'Use Redux', 'Read the docs' ]
```

Tips:

+ 应用中不要创建多个 store！相反，使用 combineReducers 来把多个 reducer 创建成一个根 reducer。

+ 你可以决定 state 的格式。你可以使用普通对象或者 Immutable 这类的实现。如果你不知道如何做，刚开始可以使用普通对象。

+ 如果 state 是普通对象，永远不要修改它！比如，reducer 里不要使用 Object.assign(state, newData)，应该使用 Object.assign({}, state, newData)。这样才不会覆盖旧的 state。如果可以的话，也可以使用 对象拓展操作符（object spread spread operator 特性中的 return { ...state, ...newData }。

+ 对于服务端运行的同构应用，为每一个请求创建一个 store 实例，以此让 store 相隔离。dispatch 一系列请求数据的 action 到 store 实例上，等待请求完成后再在服务端渲染应用。

+ 当 store 创建后，Redux 会 dispatch 一个 action 到 reducer 上，来用初始的 state 来填充 store。你不需要处理这个 action。但要记住，如果第一个参数也就是传入的 state 是 undefined 的话，reducer 应该返回初始的 state 值。

+ 要使用多个 store 增强器的时候，你可能需要使用 compose


## Store

Store 就是用来维持应用所有的 state 树 的一个对象。 改变 store 内 state 的惟一途径是对它 dispatch 一个 action。

#### Store 方法
+ getState()
+ dispatch(action)
+ subscribe(listener)
+ replaceReducer(nextReducer)


### `getState()`

返回应用当前的 state 树。
它与 store 的最后一个 reducer 返回值相同。


###  `dispatch(action)`

分发 action。这是触发 state 变化的惟一途径。    

会使用当前 `getState()` 的结果和传入的 action 以同步方式的调用 store 的 reduce 函数。返回值会被作为下一个 state。从现在开始，这就成为了 `getState()` 的返回值，同时变化监听器(change listener)会被触发。

**参数**

+ `action`*(Object†)*:描述应用变化的普通对象。按照约定，action 具有 type 字段来表示它的类型。好使用字符串，而不是 Symbols 作为 action，因为字符串是可以被序列化的。除了 type 字段外，action 对象的结构完全取决于你。


**返回值**

(Object†): 要 dispatch 的 action。

```js
import { createStore } from 'redux'

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([action.text])
    default:
      return state
  }
}

let store = createStore(todos, [ 'Use Redux' ])

function addTodo(text) {
  return {
    type: 'ADD_TODO',
    text
  }
}

store.dispatch(addTodo('Read the docs'))
store.dispatch(addTodo('Read about the middleware'))
```

**注意** *不懂*
† 使用 createStore 创建的 “纯正” store 只支持普通对象类型的 action，而且会立即传到 reducer 来执行。

但是，如果你用 applyMiddleware 来套住 createStore 时，middleware 可以修改 action 的执行，并支持执行 dispatch intent（意图）。Intent 一般是异步操作如 Promise、Observable 或者 Thunk。

Middleware 是由社区创建，并不会同 Redux 一起发行。你需要手动安装 redux-thunk 或者 redux-promise 库。你也可以创建自己的 middleware。

想学习如何描述异步 API 调用？看一下 action 创建函数里当前的 state，执行一个有副作用的操作，或者以链式操作执行它们，参照 applyMiddleware 中的示例。


### `subscribe(listener)`

添加一个变化监听器。每当 dispatch action 的时候就会执行，state 树中的一部分可能已经变化。你可以在回调函数里调用 `getState()` 来拿到当前 state。

**参数**
+ `listener` *(Function)*: 每当 dispatch action 的时候都会执行的回调。你可以在回调函数里调用 getState() 来拿到当前 state。store 的 reducer 应该是纯函数，因此你可能需要对 state 树中的引用做深度比较来确定它的值是否有变化。


返回值
(*Function*): 一个可以解绑变化监听器的函数。

```js
function select(state) {
  return state.some.deep.property
}

let currentValue
function handleChange() {
  let previousValue = currentValue
  currentValue = select(store.getState())

  if (previousValue !== currentValue) {
    console.log('Some deep nested property changed from', previousValue, 'to', currentValue)
  }
}

let unsubscribe = store.subscribe(handleChange)
unsubscribe()
```

你可以在变化监听器里面进行 `dispatch()`，但你需要注意下面的事项：
+ 监听器调用 `dispatch()` 仅仅应当发生在响应用户的 actions 或者特殊的条件限制下（比如： 在 store 有一个特殊的字段时 dispatch action）。虽然没有任何条件去调用 dispatch() 在技术上是可行的，但是随着每次 dispatch() 改变 store 可能会导致陷入无穷的循环。
+ 订阅器（subscriptions） 在每次 dispatch() 调用之前都会保存一份快照。当你在正在调用监听器（listener）的时候订阅(subscribe)或者去掉订阅（unsubscribe），对当前的 dispatch() 不会有任何影响。但是对于下一次的 dispatch()，无论嵌套与否，都会使用订阅列表里最近的一次快照。
+ 订阅器不应该注意到所有 state 的变化，在订阅器被调用之前，往往由于嵌套的 dispatch() 导致 state 发生多次的改变。保证所有的监听器都注册在 dispatch() 启动之前，这样，在调用监听器的时候就会传入监听器所存在时间里最新的一次 state。

这是一个底层 API。多数情况下，你不会直接使用它，会使用一些 React（或其它库）的绑定。如果你想让回调函数执行的时候使用当前的 state，你可以 把 store 转换成一个 Observable 或者写一个定制的 observeStore 工具。

如果需要解绑这个变化监听器，执行 `subscribe` 返回的函数即可。