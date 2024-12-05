#### 全局API

### createApp
```js
import { createApp, h, nextTick } from 'vue'

const app = createApp({})
```

### h
返回一个”虚拟节点“，通常缩写为 **VNode**：一个普通对象，其中包含向 Vue 描述它应在页面上渲染哪种节点的信息，包括所有子节点的描述。

**参数**
+ type
  - 类型：String | Object | Function
  - 详细：HTML 标签名、组件、异步组件或函数式组件。使用返回 null 的函数将渲染一个注释。此参数是必需的。
+ props
  - 类型：Object
  - 详细：一个对象，与我们将在模板中使用的 attribute、prop 和事件相对应。可选。
+ children
  - 类型：String | Array | Object
  - 详细：子代 VNode，使用 h() 生成，或者使用字符串来获取“文本 VNode”，或带有插槽的对象。可选。
  - 
    ```js
      h('div', {}, [
        'Some text comes first.',
        h('h1', 'A headline'),
        h(MyComponent, {
          someProp: 'foobar'
        })
      ])
    ```



### defineComponent

从实现上看，defineComponent 只返回传递给它的对象。但是，就类型而言，返回的值有一个合成类型的构造函数，用于手动渲染函数、TSX 和 IDE 工具支持。

```js
import { defineComponent } from 'vue'

const MyComponent = defineComponent({
  data() {
    return { count: 1 }
  },
  methods: {
    increment() {
      this.count++
    }
  }
})
```

或者是一个 `setup` 函数，函数名称将作为组件名称来使用
```js
import { defineComponent, ref } from 'vue'

const HelloWorld = defineComponent(function HelloWorld() {
  const count = ref(0)
  return { count }
})
```

`ts` + `setup`
```ts
<script lang="ts">
export default {
  name: "componentName",
  setup() {}
}
</script>
```


### defineAsyncComponent
创建一个只有在需要时才会加载的异步组件

可以接受一个返回 Promise 的工厂函数。`Promise` 的 `resolve` 回调应该在服务端返回组件定义后被调用。你也可以调用 `reject(reason)` 来表示加载失败。
```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() =>
  import('./components/AsyncComponent.vue')
)

app.component('async-component', AsyncComp)
```

```js
import { createApp, defineAsyncComponent } from 'vue'

createApp({
  // ...
  components: {
    AsyncComponent: defineAsyncComponent(() =>
      import('./components/AsyncComponent.vue')
    )
  }
})
```

高阶用法
```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent({
  // 工厂函数
  loader: () => import('./Foo.vue'),
  // 加载异步组件时要使用的组件
  loadingComponent: LoadingComponent,
  // 加载失败时要使用的组件
  errorComponent: ErrorComponent,
  // 在显示 loadingComponent 之前的延迟 | 默认值：200（单位 ms）
  delay: 200,
  // 如果提供了 timeout ，并且加载组件的时间超过了设定值，将显示错误组件
  // 默认值：Infinity（即永不超时，单位 ms）
  timeout: 3000,
  // 定义组件是否可挂起 | 默认值：true
  suspensible: false,
  /**
   *
   * @param {*} error 错误信息对象
   * @param {*} retry 一个函数，用于指示当 promise 加载器 reject 时，加载器是否应该重试
   * @param {*} fail  一个函数，指示加载程序结束退出
   * @param {*} attempts 允许的最大重试次数
   */
  onError(error, retry, fail, attempts) {
    if (error.message.match(/fetch/) && attempts <= 3) {
      // 请求发生错误时重试，最多可尝试 3 次
      retry()
    } else {
      // 注意，retry/fail 就像 promise 的 resolve/reject 一样：
      // 必须调用其中一个才能继续错误处理。
      fail()
    }
  }
})
```

<!-- 参考：动态和异步组件 -->



### defineCustomElement(3.2+)
该方法接受和 defineComponent 相同的参数，但是返回一个原生的自定义元素，该元素可以用于任意框架或不基于框架使用。

```html
<my-vue-element></my-vue-element>
```
```js
import { defineCustomElement } from 'vue'
const MyVueElement = defineCustomElement({
  // 这里是普通的 Vue 组件选项
  props: {},
  emits: {},
  template: `...`,
  // 只用于 defineCustomElement：注入到 shadow root 中的 CSS
  styles: [`/* inlined css */`]
})
// 注册该自定义元素。
// 注册过后，页面上所有的 `<my-vue-element>` 标记会被升级。
customElements.define('my-vue-element', MyVueElement)
// 你也可以用编程的方式初始化这个元素：
// (在注册之后才可以这样做)
document.body.appendChild(
  new MyVueElement({
    // 初始化的 prop (可选)
  })
)
```

<!-- 关于使用 Vue，尤其是通过单文件组件构建 Web Components 的更多细节，请查阅Vue 和 Web Components。 -->



### resolvecomponent

!> `resolveComponent` 只能在 `render` 或 `setup` 函数中使用。

如果在当前应用实例中可用，则允许按名称解析 `component`。

返回一个 `Component`。如果没有找到，则返回接收的参数 `name`。
```js
const app = createApp({})
app.component('MyComponent', {
  /* ... */
})
```
```js
import { resolveComponent } from 'vue'
render() {
  const MyComponent = resolveComponent('MyComponent')
}
```

**参数**
+ name
  - 类型：String
  - 详细：已加载的组件的名称。



### resolveDynamicComponent

!> `resolveDynamicComponent` 只能在 `render` 或 `setup` 函数中使用。

允许使用与 `<component :is="">` 相同的机制来解析一个 `component`

返回已解析的 `Component` 或新创建的 `VNode`，其中组件名称作为节点标签。如果找不到 `Component`，将发出警告。
```js
import { resolveDynamicComponent } from 'vue'
render () {
  const MyComponent = resolveDynamicComponent('MyComponent')
}
```

**参数**
+ component
  - 类型：String | Object (组件的选项对象)
  <!-- - 详细：有关详细信息，请参阅动态组件上的文档。 -->



### resolveDirective

!> `resolveDirective` 只能在 `render` 或 `setup` 函数中使用。

如果在当前应用实例中可用，则允许通过其名称解析一个 directive。

返回一个 Directive。如果没有找到，则返回 undefined。
```js
const app = createApp({})
app.directive('highlight', {})
```
```js
import { resolveDirective } from 'vue'
render () {
  const highlightDirective = resolveDirective('highlight')
}
```

**参数**
+ name
  - 类型：String
  - 详细：已加载的指令的名称。



### withDirectives

!> `withDirectives` 只能在 `render` 或 `setup` 函数中使用。

**允许将指令应用于 VNode。返回一个包含应用指令的 VNode。**

```js
import { withDirectives, resolveDirective } from 'vue'
const foo = resolveDirective('foo')
const bar = resolveDirective('bar')

return withDirectives(h('div'), [
  [foo, this.x],
  [bar, this.y]
])
```

**参数**
+ vnode
  - 类型：vnode
  - 详细：一个虚拟节点，通常使用 h() 创建。

+ directives
  - 类型：Array
  - 详细：一个指令数组。
    - `[directive]` - 该指令本身。必选。
      ```js
      const MyDirective = resolveDirective('MyDirective')
      const nodeWithDirectives = withDirectives(h('div'), [[MyDirective]])
      ```
    - `[directive, value]` - 上述内容，再加上分配给指令的类型为 `any` 的值。
      ```js
      const MyDirective = resolveDirective('MyDirective')
      const nodeWithDirectives = withDirectives(h('div'), [[MyDirective, 100]])
      ```
    - `[directive, value, arg]` - 上述内容，再加上一个 `string` 参数，比如：在 v-on:click 中的 click。
      ```js
      const MyDirective = resolveDirective('MyDirective')
      const nodeWithDirectives = withDirectives(h('div'), [
        [MyDirective, 100, 'click']
      ])
      ```
    - `[directive, value, arg, modifiers]` - 上述内容，再加上定义任何修饰符的 `key: value` 键值对 `Object`。
      ```js
      const MyDirective = resolveDirective('MyDirective')
      const nodeWithDirectives = withDirectives(h('div'), [
        [MyDirective, 100, 'click', { prevent: true }]
      ])
      ```



### createRenderer

createRenderer 函数接受两个泛型参数： `HostNode` 和 `HostElement`，对应于宿主环境中的 Node 和 Element 类型。

例如，对于 runtime-dom，HostNode 将是 DOM Node 接口，HostElement 将是 DOM Element 接口。

自定义渲染器可以传入特定于平台的类型，如下所示：
```js
import { createRenderer } from 'vue'
const { render, createApp } = createRenderer<Node, Element>({
  patchProp,
  ...nodeOps
})
```

**参数**
+ HostNode
  - 类型：Node
  - 详细：宿主环境中的节点。

+ HostElement
  - 类型：Element
  - 详细：宿主环境中的元素。



### nextTick

将回调推迟到下一个 DOM 更新周期之后执行。在更改了一些数据以等待 DOM 更新后立即使用它。
```js
import { createApp, nextTick } from 'vue'

const app = createApp({
  setup() {
    const message = ref('Hello!')
    const changeMessage = async newMessage => {
      message.value = newMessage
      await nextTick()
      console.log('Now DOM is updated')
    }
  }
})
```

不同的是回调的 this 自动绑定到调用它的实例上
```js
createApp({
  // ...
  methods: {
    // ...
    example() {
      // 修改数据
      this.message = 'changed'
      // DOM 尚未更新
      this.$nextTick(function() {
        // DOM 现在更新了
        // `this` 被绑定到当前实例
        this.doSomethingElse()
      })
    }
  }
})
```



### mergeProps

将包含 VNode prop 的多个对象合并为一个单独的对象。其返回的是一个新创建的对象，而作为参数传递的对象则不会被修改。

可以传递不限数量的对象，后面参数的 property 优先。事件监听器被特殊处理，`class` 和 `style` 也是如此，这些 property 的值是被合并的而不是覆盖的。
```js
import { h, mergeProps } from 'vue'
export default {
  inheritAttrs: false,
  render() {
    const props = mergeProps(
      {
        // 该 class 将与 $attrs 中的其他 class 合并。
        class: 'active'
      },
      this.$attrs
    )

    return h('div', props)
  }
}
```



### useCssModule

!> `useCssModule` 只能在 `render` 或 `setup` 函数中使用。

```js
<script>
import { h, useCssModule } from 'vue'
export default {
  setup() {
    const style = useCssModule()
    return () =>
      h(
        'div',
        {
          class: style.success
        },
        'Task complete!'
      )
  }
}
</script>
<style module>
.success {
  color: #090;
}
</style>
```

**参数**
+ name名词
  - 类型： String
  - 详细：CSS 模块的名称。默认为 '$style'。

<!-- 关于使用 CSS 模块的更多信息，请参阅 单文件组件样式特性：<style module>  -->



### version

以字符串形式提供已安装的 Vue 的版本号。

```js
const version = Number(Vue.version.split('.')[0])
if (version === 3) {
  // Vue 3
} else if (version === 2) {
  // Vue 2
} else {
  // 不支持的 Vue 的版本
}
```