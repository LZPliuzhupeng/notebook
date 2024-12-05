#### References


## createRef

createRef函数将返回一个只有一个属性的普通对象:current。每当调用render方法时，Preact会将DOM节点或组件赋值为current。
```js
class Foo extends Component {
  ref = createRef();

  componentDidMount() {
    console.log(this.ref.current);
    // Logs: [HTMLDivElement]
  }

  render() {
    return <div ref={this.ref}>foo</div>
  }
}
```


## Callback Refs
```js
class Foo extends Component {
  ref = null;
  setRef = (dom) => this.ref = dom;

  componentDidMount() {
    console.log(this.ref);
    // Logs: [HTMLDivElement]
  }

  render() {
    return <div ref={this.setRef}>foo</div>
  }
}
```

!> 如果ref回调被定义为内联函数，它将被调用两次。一次使用null，一次使用实际引用。这是一个常见的错误，createRef API通过强制用户检查ref.current是否被定义，使这个问题变得更容易一些。


## 把它们放在一起

```js
class Foo extends Component {
  // We want to use the real width from the DOM node here
  state = {
    width: 0,
    height: 0,
  };

  render(_, { width, height }) {
    return <div>Width: {width}, Height: {height}</div>;
  }
}
```
只有在调用渲染方法并将组件挂载到DOM中时，度量才有意义。在此之前，DOM节点不存在，因此度量它没有多大意义。
```js
class Foo extends Component {
  state = {
    width: 0,
    height: 0,
  };

  ref = createRef();

  componentDidMount() {
    // For safety: Check if a ref was supplied
    if (this.ref.current) {
      const dimensions = this.ref.current.getBoundingClientRect();
      this.setState({
        width: dimensions.width,
        height: dimensions.height,
      });
    }
  }

  render(_, { width, height }) {
    return (
      <div ref={this.ref}>
        Width: {width}, Height: {height}
      </div>
    );
  }
}
```
就是这样!现在组件在安装时将始终显示宽度和高度。 