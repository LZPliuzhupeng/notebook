### 自定义双向绑定（proxy）

```
<div id="app">
      <input type="text" id="input" />
      <div>您输入的是： <span id="title"></span></div>
 </div>

```

```js
const obj = {};
const input = document.getElementById("input");
const title = document.getElementById("title");

const newObj = new Proxy(obj, {
  get: function (target, key, receiver) {
    console.log(`getting ${key}!`);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, value, receiver) {
    console.log(target, key, value, receiver);
    if (key === "text") {
      input.value = value;
      title.innerHTML = value;
    }
    return Reflect.set(target, key, value, receiver);
  },
});

input.addEventListener("keyup", function (e) {
  newObj.text = e.target.value;
});
```

> `Proxy`方法拦截`target`对象的属性赋值行为。它采用`Reflect.set`方法将值赋值给对象的属性，确保完成原有的行为。

### setup 和 defineComponent

### reactive()

### ref()

### toRefs()

### UnwrapRef<>

### defineExpose()

### v-once 和 v-memo

### defineAsyncComponent

### watchEffect 和 watch
