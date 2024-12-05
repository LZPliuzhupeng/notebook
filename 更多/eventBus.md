# 事件总线（发布订阅模式）


### 例子1
```js
var broadcast = {
  // 通过调用 broadcast.on 注册事件。其他页面都可以通过调用 broadcast.fire 触发该事件
  // 参数说明：如果 isUniq 为 true，该注册事件将唯一存在；如果值为 false或者没有传值，每注册一个事件都将会被存储下来
  on: function (name, fn, isUniq) {
    this._on(name, fn, isUniq, false)
  },
  // 通过调用 broadcast.once 注册的事件，在触发一次之后就会被销毁
  once: function (name, fn, isUniq) {
    this._on(name, fn, isUniq, true)
  },
  _on: function (name, fn, isUniq, once) {
    var eventData
    eventData = broadcast.data
    var fnObj = {
      fn: fn,
      once: once
    }
    if (!isUniq && eventData.hasOwnProperty(name)) {
      eventData[name].push(fnObj)
    } else {
      eventData[name] = [fnObj]
    }
    return this
  },
  // 触发事件
  // 参数说明：name 表示事件名，data 表示传递给事件的参数
  fire: function (name, data, thisArg) {
    // console.log('[broadcast fire]: ' + name, data)
    var fn, fnList, i, len
    thisArg = thisArg || null
    fnList = broadcast.data[name] || []
    if (fnList.length) {
      for (i = 0, len = fnList.length; i < len; i++) {
        fn = fnList[i].fn
        fn.apply(thisArg, [data, name])
        if (fnList[i].once) {
          fnList.splice(i, 1)
          i--
          len--
        }
      }
    }
    return this
  },
  data: {}
}
module.exports = broadcast  
```

### 例子2
```js
class EventEmitter {
    constructor() {
        this.cache = {}
    }
    on(name, fn) {
        if (this.cache[name]) {
            this.cache[name].push(fn)
        } else {
            this.cache[name] = [fn]
        }
    }
    off(name, fn) {
        let tasks = this.cache[name]
        if (tasks) {
            const index = tasks.findIndex(f => f === fn || f.callback === fn)
            if (index >= 0) {
                tasks.splice(index, 1)
            }
        }
    }
    emit(name, once = false, ...args) {
        if (this.cache[name]) {
            // 创建副本，如果回调函数内继续注册相同事件，会造成死循环
            let tasks = this.cache[name].slice()
            for (let fn of tasks) {
                fn(...args)
            }
            if (once) {
                delete this.cache[name]
            }
        }
    }
}

// 测试
let eventBus = new EventEmitter()
let fn1 = function(name, age) {
    console.log(`${name} ${age}`)
}
let fn2 = function(name, age) {
    console.log(`hello, ${name} ${age}`)
}
eventBus.on('aaa', fn1)
eventBus.on('aaa', fn2)
eventBus.emit('aaa', false, '布兰', 12)
```