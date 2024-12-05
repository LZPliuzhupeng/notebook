### async/await

*async/await可以说是Promise的语法糖*


+ promise

```
fetch('coffee.jpg')
    .then(response => response.blob())
    .then(myBlob => {
          let objectURL = URL.createObjectURL(myBlob)
          let image = document.createElement('img')
          image.src = objectURL
          document.body.appendChild(image)
    })
    .catch(e => {
          console.log('There has been a problem with your fetch operation: ' + e.message)
    })

```

+ async/await

```
async function myFetch() {
      let response = await fetch('coffee.jpg')
      let myBlob = await response.blob()

      let objectURL = URL.createObjectURL(myBlob)
      let image = document.createElement('img')
      image.src = objectURL
      document.body.appendChild(image)
}

myFetch()

```
> 同try{}catch{}捕捉

### Object.values() / Object.keys()

`Object.values()`方法返回一个给定对象自身的所有可枚举属性值的数组

区别在于 `for-in` 循环枚举原型链中的属性 

```
const object1 = {
      a: 'somestring',
      b: 42,
      c: false
}
console.log(Object.values(object1)) // ["somestring", 42, false]
console.log(Object.keys(object1)) // ["a", "b", "c"]
```
### Object.entries()

`Object.entries()`方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 for...in 循环遍历该对象时返回的顺序一致（区别在于 `for-in` 循环还会枚举原型链中的属性）。

```
Object.entries({
      a: 'somestring',
      b: 42
})

//[ ["a", "somestring"], ["b", 42]  ]
```

### Object.fromEntries()

`Object.fromEntries()` 方法把键值对列表转换为一个对象，它是`Object.entries()`的反函数。

```
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
])

const obj = Object.fromEntries(entries)

console.log(obj) // Object { foo: "bar", baz: 42 }

```

### Object.getOwnPropertyDescriptors()

`Object.getOwnPropertyDescriptors()` 方法用来获取一个对象的所有自身属性的描述符。代码如下：

```
const object1 = {
  property1: 42
}

const descriptors1 = Object.getOwnPropertyDescriptors(object1)

console.log(descriptors1.property1.writable) // true

console.log(descriptors1.property1.value) // 42

```


### Promise.prototype.finally()

`finally()`方法会返回一个`Promise`，当`promise`的状态变更，不管是变成`rejected`或者`fulfilled`，最终都会执行`finally()`的回调。

```
fetch(url)
      .then((res) => {
        console.log(res)
      })
      .catch((error) => { 
        console.log(error)
      })
      .finally(() => { 
        console.log('结束')
    })

```


### String.prototype.trimStart() / trimLeft() / trimEnd() / trimRight()

在ES5中可以通过`trim()`来去掉字符首尾的空格，但是却无法只去掉单边的

如果我们要去掉开头的空格，可以使用trimStart()或者它的别名trimLeft()，

```
const Str = '   Hello world!  '
console.log(Str) // '   Hello world!  '
console.log(Str.trimStart()) // 'Hello world!  '
console.log(Str.trimLeft()) // 'Hello world!  '
console.log(Str.trimEnd()) // '   Hello world!'
console.log(Str.trimRight()) // '   Hello world!'

```

> 不过这里有一点要注意的是，`trimStart()`跟`trimEnd()`才是标准方法，`trimLeft()`跟`trimRight()`只是别名。