#### Hooks

## 介绍

```js
class Counter extends Component {
  state = {
    value: 0
  };

  increment = () => {
    this.setState(prev => ({ value: prev.value +1 }));
  };

  render(props, state) {
    return (
      <div>
        Counter: {state.value}
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

```js
function Counter() {
  const [value, setValue] = useState(0);
  const increment = useCallback(() => {
    setValue(value + 1);
  }, [value]);

  return (
    <div>
      Counter: {value}
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

在这一点上，它们看起来非常相似，但是我们可以进一步简化钩子版本。让我们将计数器逻辑提取到一个自定义钩子中，使其易于跨组件重用:

```js
function useCounter() {
  const [value, setValue] = useState(0);
  const increment = useCallback(() => {
    setValue(value + 1);
  }, [value]);
  return { value, increment };
}

// First counter
function CounterA() {
  const { value, increment } = useCounter();
  return (
    <div>
      Counter A: {value}
      <button onClick={increment}>Increment</button>
    </div>
  );
}

// Second counter which renders a different output.
function CounterB() {
  const { value, increment } = useCounter();
  return (
    <div>
      <h1>Counter B: {value}</h1>
      <p>I'm a nice counter</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

## 依赖参数

许多钩子接受一个参数，该参数可以用来限制钩子何时应该更新。Preact检查依赖数组中的每个值，并检查自上次调用钩子以来是否有变化。当依赖参数未指定时，钩子总是被执行。

在上面的useCounter()实现中，我们传递了一个依赖数组给useCallback():

```js
function useCounter() {
  const [value, setValue] = useState(0);
  const increment = useCallback(() => {
    setValue(value + 1);
  }, [value]);  // <-- the dependency array
  return { value, increment };
}
```
在这里传递值会导致useCallback在值改变时返回一个新的函数引用。这是必要的，以避免“陈旧的闭包”，其中回调将总是引用第一个渲染的值变量，从它创建时，导致增量总是设置1的值。

?> 这将在每次值更改时创建一个新的增量回调。出于性能原因，使用回调来更新状态值通常比使用依赖项保留当前值更好。


## 状态钩子


### useState

```js
import { h } from 'preact';
import { useState } from 'preact/hooks';

const Counter = () => {
  const [count, setCount] = useState(0);
  const increment = () => setCount(count + 1);
  // You can also pass a callback to the setter
  const decrement = () => setCount((currentCount) => currentCount - 1);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  )
}
```

?> 当我们的初始状态很昂贵时，最好传递一个函数而不是一个值。


###  useReducer

useReducer钩子与redux非常相似。与useState相比，当你有复杂的状态逻辑，即下一个状态依赖于前一个状态时，它更容易使用。

```js
const initialState = 0;
const reducer = (state, action) => {
  switch (action) {
    case 'increment': return state + 1;
    case 'decrement': return state - 1;
    case 'reset': return 0;
    default: throw new Error('Unexpected action');
  }
};

function Counter() {
  // Returns the current state and a dispatch function to
  // trigger an action
  const [count, dispatch] = useReducer(reducer, initialState);
  return (
    <div>
      {count}
      <button onClick={() => dispatch('increment')}>+1</button>
      <button onClick={() => dispatch('decrement')}>-1</button>
      <button onClick={() => dispatch('reset')}>reset</button>
    </div>
  );
}
```


## Memoization(记忆化)

### useMemo

使用useMemo钩子，我们可以记住计算的结果，只有当其中一个依赖项发生变化时才重新计算。

```js
const memoized = useMemo(
  () => expensive(a, b),
  // Only re-run the expensive function when any of these
  // dependencies change
  [a, b]
);
```

?> Don't run any effectful code inside useMemo. Side-effects belong in useEffect.(不要在useMemo中运行任何有效的代码。副作用属于useEffect)


### useCallback

useCallback钩子可以用来确保只要没有依赖关系发生变化，返回的函数在引用上就保持相等。这可以用于优化子组件的更新，当它们依赖于引用相等来跳过更新时(例如shouldComponentUpdate)。

```js
const onClick = useCallback(
  () => console.log(a, b),
  [a, b]
);
```

?> useCallback(fn, deps)等价于useMemo(() => fn, deps)


## useRef

要在功能组件中获取对DOM节点的引用，可以使用useRef钩子。它的工作原理类似于createRef。

```js
function Foo() {
  // Initialize useRef with an initial value of `null`
  const input = useRef(null);
  const onClick = () => input.current && input.current.focus();

  return (
    <>
      <input ref={input} />
      <button onClick={onClick}>Focus input</button>
    </>
  );
}
```

?> 注意不要将useRef与createRef混淆


## useContext

要访问函数组件中的上下文，我们可以使用usecontext钩子，而不需要任何高阶或包装器组件。第一个参数必须是从createContext调用创建的上下文对象。

```js
const Theme = createContext('light');

function DisplayTheme() {
  const theme = useContext(Theme);
  return <p>Active theme: {theme}</p>;
}

// ...later
function App() {
  return (
    <Theme.Provider value="light">
      <OtherComponent>
        <DisplayTheme />
      </OtherComponent>
    </Theme.Provider>
  )
}
```


## Side-Effects(副作用)

### useEffect

```js
useEffect(() => {
  // Trigger your effect
  return () => {
    // Optional: Any cleanup code
  };
}, []);
```
```js
function PageTitle(props) {
  useEffect(() => {
    document.title = props.title;
  }, [props.title]);

  return <h1>{props.title}</h1>;
}
```

useEffect的第一个参数是触发效果的无参数回调。在我们的例子中，我们只希望在标题真正改变时触发它。如果它保持不变，就没有必要更新了。这就是为什么我们使用第二个参数来指定我们的依赖数组。

但有时我们有一个更复杂的用例。设想一个组件，它在挂载时需要订阅一些数据，在卸载时需要取消订阅。这也可以通过useEffect来完成。要运行任何清理代码，我们只需要在回调中返回一个函数。

```js
// Component that will always display the current window width
function WindowWidth(props) {
  const [width, setWidth] = useState(0);

  function onResize() {
    setWidth(window.innerWidth);
  }

  useEffect(() => {
    window.addEventListener('resize', onResize);
    return () => window.removeEventListener('resize', onResize);
  }, []);

  return <div>Window width: {width}</div>;
}
```

?> 清理功能为可选功能。如果你不需要运行任何清理代码，你就不需要在传递给useEffect的回调中返回任何东西。


### useLayoutEffect

该签名与useEffect相同，但一旦组件发生差异且浏览器有机会绘制，它就会触发。


### useErrorBoundary

每当子组件抛出错误时，您可以使用此钩子捕获它并向用户显示定制错误Ul。    
```js
// error = The error that was caught or `undefined` if nothing errored.
// resetError = Call this function to mark an error as resolved. It's
//   up to your app to decide what that means and if it is possible
//   to recover from errors.
const [error, resetError] = useErrorBoundary();
```

为了监视的目的，通知服务任何错误通常都是非常有用的。为此，我们可以利用一个可选回调函数，并将其作为第一个参数传递给useErrorBoundary。
```js
const [error] = useErrorBoundary(error => callMyApi(error.message));
```

```js
const App = props => {
  const [error, resetError] = useErrorBoundary(
    error => callMyApi(error.message)
  );

  // Display a nice error message
  if (error) {
    return (
      <div>
        <p>{error.message}</p>
        <button onClick={resetError}>Try again</button>
      </div>
    );
  } else {
    return <div>{props.children}</div>
  }
};
```