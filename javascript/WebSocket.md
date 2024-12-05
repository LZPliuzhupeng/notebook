
## WebSocket与HTTP

+ HTTP 协议有一个缺陷：通信只能由客户端发起。

+ WebSocket最大特点就是：服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。

  - 其他特点

  - 建立在 TCP 协议之上，服务器端的实现比较容易。

  - 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

  - 数据格式比较轻量，性能开销小，通信高效。

  - 可以发送文本，也可以发送二进制数据。

  - 没有同源限制，客户端可以与任意服务器通信。

  - 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。


## WebSocket 构造函数

```js
var ws=new WebSocket("ws://echo.websocket.org");//官网测试链接
```

## webSocket.readyState

`readyState`属性返回实例对象的当前状态，共有四种。

+ `CONNECTING`：值为0，表示正在连接。
+ `OPEN`：值为1，表示连接成功，可以通信了。
+ `CLOSING`：值为2，表示连接正在关闭。
+ `CLOSED`：值为3，表示连接已经关闭，或者打开连接失败。



## webSocket.onopen

实例对象的onopen属性，用于指定连接成功后的回调函数。

```js
ws.onopen = function () {
  ws.send('Hello Server!');
}
```

如果要指定多个回调函数，可以使用addEventListener方法。
```js
socket.onerror = function(event) {
  // handle error event
};

socket.addEventListener("error", function(event) {
  // handle error event
});

```

## webSocket.onclose

实例对象的onclose属性，用于指定连接关闭后的回调函数。

```js
ws.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
};
```

## webSocket.onmessage

实例对象的onmessage属性，用于指定收到服务器数据后的回调函数。

```js
ws.onmessage = function(event) {
  var data = event.data;
  // 处理数据
};

```

服务器数据可能是文本，也可能是二进制数据（blob对象或Arraybuffer对象）。
```js
ws.onmessage = function(event){
  if(typeof event.data === String) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```

也可以使用binaryType属性，显式指定收到的二进制数据类型
```js
// 收到的是 blob 数据
ws.binaryType = "blob";
ws.onmessage = function(e) {
  console.log(e.data.size);
};

// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
  console.log(e.data.byteLength);
};
```

## webSocket.send()

实例对象的send()方法用于向服务器发送数据。
```js
ws.send('your message');
```

发送 Blob 对象的例子。
```js
var file = document
  .querySelector('input[type="file"]')
  .files[0];
ws.send(file);
```

发送 ArrayBuffer 对象的例子。
```js
// Sending canvas ImageData as ArrayBuffer
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
ws.send(binary.buffer);
```

## webSocket.bufferedAmount

实例对象的bufferedAmount属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。
```js
var data = new ArrayBuffer(10000000);
socket.send(data);

if (socket.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```

## webSocket.onerror

实例对象的onerror属性，用于指定报错时的回调函数。
```js
socket.onerror = function(event) {
  // handle error event
};

socket.addEventListener("error", function(event) {
  // handle error event
});
```


## WebSocket心跳及重连机制
 在使用websocket的过程中，有时候会遇到网络断开的情况，但是在网络断开的时候服务器端并没有触发onclose的事件。这样会有：服务器会继续向客户端发送多余的链接，并且这些数据还会丢失。所以就需要一种机制来检测客户端和服务端是否处于正常的链接状态。因此就有了websocket的心跳了。还有心跳，说明还活着，没有心跳说明已经挂掉了。

* 1. 为什么叫心跳包呢？
它就像心跳一样每隔固定的时间发一次，来告诉服务器，我还活着。

* 2. 心跳机制是？
心跳机制是每隔一段时间会向服务器发送一个数据包，告诉服务器自己还活着，同时客户端会确认服务器端是否还活着，如果还活着的话，就会回传一个数据包给客户端来确定服务器端也还活着，否则的话，有可能是网络断开连接了。需要重连~


**具体的思路如下：**
* 1. 第一步页面初始化，先调用createWebSocket函数，目的是创建一个websocket的方法：new WebSocket(wsUrl);因此封装成函数内如下代码：
```js
function createWebSocket() {
  try {
    ws = new WebSocket(wsUrl);
    init();
  } catch(e) {
    console.log('catch');
    reconnect(wsUrl);
  }
}
```

* 2. 第二步调用init方法，该方法内把一些监听事件封装如下：
```js
function init() {
  ws.onclose = function () {
    console.log('链接关闭');
    reconnect(wsUrl);
  };
  ws.onerror = function() {
    console.log('发生异常了');
    reconnect(wsUrl);
  };
  ws.onopen = function () {
    //心跳检测重置
    heartCheck.start();
  };
  ws.onmessage = function (event) {
    //拿到任何消息都说明当前连接是正常的
    console.log('接收到消息');
    heartCheck.start();
  }
}
```

* 3. 如上第二步，当网络断开的时候，会先调用onerror，onclose事件可以监听到，会调用reconnect方法进行重连操作。正常的情况下，是先调用onopen方法的，当接收到数据时，会被onmessage事件监听到。

* 4. 重连操作 reconnect代码如下：
```js
var lockReconnect = false;//避免重复连接
function reconnect(url) {
  if(lockReconnect) {
    return;
  };
  lockReconnect = true;
  //没连接上会一直重连，设置延迟避免请求过多
  tt && clearTimeout(tt);
  tt = setTimeout(function () {
    createWebSocket(url);
    lockReconnect = false;
  }, 4000);
}
```

如上代码，如果网络断开的话，会执行reconnect方法，使用了一个定时器，4秒后会重新创建一个新的websocket链接，重新调用createWebSocket函数，重新会执行及发送数据给服务器端。


* 5. 最后一步就是实现心跳检测的代码：
```js
//心跳检测
var heartCheck = {
  timeout: 3000,
  timeoutObj: null,
  serverTimeoutObj: null,
  start: function(){
    console.log('start');
    var self = this;
    this.timeoutObj && clearTimeout(this.timeoutObj);
    this.serverTimeoutObj && clearTimeout(this.serverTimeoutObj);
    this.timeoutObj = setTimeout(function(){
      //这里发送一个心跳，后端收到后，返回一个心跳消息，
      //onmessage拿到返回的心跳就说明连接正常
      console.log('55555');
      ws.send("123456789");
      self.serverTimeoutObj = setTimeout(function() {
        console.log(111);
        console.log(ws);
        ws.close();
        // createWebSocket();
      }, self.timeout);

    }, this.timeout)
  }
}
```

实现心跳检测的思路是：每隔一段固定的时间，向服务器端发送一个ping数据，如果在正常的情况下，服务器会返回一个pong给客户端，    
如果客户端通过    onmessage事件能监听到的话，说明请求正常，这里我们使用了一个定时器，每隔3秒的情况下，    
如果是网络断开的情况下，在指定的时间内服务器端并没有返回心跳响应消息，因此服务器端断开了，因此这个时候我们使用ws.close关闭连接，在一段时间后(在不同的浏览器下，时间是不一样的，firefox响应更快)，    
可以通过 onclose事件监听到。因此在onclose事件内，我们可以调用 reconnect事件进行重连操作。

