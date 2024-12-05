# runtime

## 权限

Runtime API 上的大多数方法都不需要任何权限，`sendNativeMessage()` 和 `connectNative()` 除外，它们需要 "nativeMessaging" 权限。

_manifest.json_

```json
{
  "name": "My extension",
  ...
  "permissions": [
    "nativeMessaging"
  ],
  ...
}
```

## Methods、Events

### connect()、onConnect

尝试连接扩展程序/应用（例如后台网页）或其他扩展程序/应用中的监听器。这对于连接到扩展程序进程的内容脚本、应用/扩展程序通信以及网络消息非常有用。请注意，这不会连接到内容脚本中的任何监听器。扩展程序可通过 tabs.connect 连接到标签页中嵌入的内容脚本。

可以让你在 Chrome 扩展的不同部分之间创建**长期的连接**，比如：在 content scripts 和 background scripts 之间或者是在不同的扩展之间。这个方法返回一个 Port 对象，你可以通过它发送和接收消息。

_type_

```ts
chrome.runtime.connect(
  extensionId?: string, // 要连接的扩展程序或应用的 ID。如果省略此参数，系统将尝试与您自己的扩展程序建立连接。
  connectInfo?: {
        includeTlsChannelId?: boolean, // 对于正在监听连接事件的进程，是否将 TLS 通道 ID 传递到 onConnectExternal
        name?: string, // 对于正在监听连接事件的进程，此参数将传递到 onConnect
    },
)

chrome.runtime.onConnect.addListener(
  callback:
  function,
)
```

_background script_

```js
chrome.runtime.onConnect.addListener(function (port) {
  port.onMessage.addListener(function (message) {
    // 处理收到的消息
    console.log(message);

    // 回复消息
    port.postMessage({ reply: "Hello from background script" });
  });
});
```

_content script_

```js
let port = chrome.runtime.connect({ name: "my-connection" });
port.postMessage({ greeting: "hello" });

port.onMessage.addListener(function (message) {
  // 处理收到的回复
  console.log(message.reply);
});
```

**区别**

- chrome.runtime.onMessage.addListener 用于处理一次性的简单交互。你发送一个请求，然后得到一个响应。这是一次性的，每次通信都需要重新发送请求。

- chrome.runtime.connect 则创建了一个持久的连接，可以发送多个消息而不需要每次都重新连接。这对于需要多次交互的功能非常有用。

### getBackgroundPage()

检索当前扩展程序/应用中运行的后台网页的 JavaScript“window”对象。如果该后台网页是事件网页，系统将确保在调用回调之前已加载该网页。如果没有后台网页，则会设置错误。

_type_

```ts
chrome.runtime.getBackgroundPage(
  callback?: function
)

type function = ( backgroundPage?: Window ) => void
```

### getURL()

将应用/扩展程序安装目录中的相对路径转换为完全限定网址。

_type_

```ts
chrome.runtime.getURL(
  path:
  string,
)
```

```js
let url = chrome.runtime.getURL("images/icon.png");
console.log(url); // 'chrome-extension://[EXTENSION_ID]/images/icon.png'
```

!> 请注意，chrome.runtime.getURL 只能访问扩展包中的文件。它不能访问扩展的私有存储空间（例如 chrome.storage.local），也不能访问 web 上的资源。

### openOptionsPage()

允许你打开扩展的选项页面

_type_

```ts
chrome.runtime.openOptionsPage(
  callback?: () => void,
)
```

```js
chrome.browserAction.onClicked.addListener(() => {
  chrome.runtime.openOptionsPage().catch((err) => console.error(err));
});
```

```json
{
  "name": "My Extension",
  "options_ui": {
    "page": "options.html",
    "open_in_tab": true // 新的浏览器标签页中打开（true），还是在一个小窗口中打开（false）
  }
}
```

### sendMessage()、onMessage

_type_

```ts
chrome.runtime.sendMessage(
  extensionId?: string, //指定接收消息的扩展的ID。如果没有提供，消息将发送到调用此方法的扩展。
  message: any, // 要发送的消息。这可以是任何可以在JSON中表示的对象或者简单数据类型。
  options?: {
    includeTlsChannelId: boolean, //对于正在监听连接事件的进程，是否将 TLS 通道 ID 传递到 onMessageExternal。
   },
  callback?: function, // 当消息的接收者响应时将调用的函数。
)
```

_content script_

```js
chrome.runtime.sendMessage(
  { content: "Hello from content script!" },
  function (response) {
    console.log(response.reply);
  }
);
```

_background script_

```js
chrome.runtime.onMessage.addListener(function (message, sender, sendResponse) {
  console.log(message.content); // "Hello from content script!"
  sendResponse({ reply: "Hello from background script!" });
});
```

这里，`sendResponse`是一个函数，它接受一个参数（响应消息），并将其发送回消息的发送者。

注意，如果你在消息的接收者中返回 true，你可以在异步方式中调用 sendResponse。否则，sendResponse 函数在消息处理函数返回后立即失效。

```js
chrome.runtime.onMessage.addListener(function (message, sender, sendResponse) {
  console.log(message.content); // "Hello from content script!"

  // 模拟异步操作
  setTimeout(function () {
    sendResponse({ reply: "Hello from background script, after a delay!" });
  }, 2000);

  return true; // 保持sendResponse有效
});
```

!> 请注意，chrome.runtime.sendMessage 和 chrome.runtime.onMessage 之间的通信是一种单向通信方式。如果你需要实现双向通信，你可能需要使用 chrome.runtime.connect 来创建一个持久的连接。

### onInstalled

在扩展首次安装、更新到新版本，或者浏览器更新到新版本时触发。 这个事件通常用于在这些情况下执行一次性的设置操作，例如初始化持久性存储，或者在更新扩展后进行一次性的数据迁移。

_type_

```ts
chrome.runtime.onInstalled.addListener(
  callback:({
    id?: string,
    previousVersion?: string,
    reason : "install"|"update"|"chrome_update"|"shared_module_update"
  })=>void
)
```

**reason**

- "install"：扩展首次安装。
- "update"：扩展更新到新版本。
- "chrome_update"：浏览器更新到新版本。
- "shared_module_update"：扩展所使用的共享模块更新到新版本。

!> chrome.runtime.onInstalled 只在扩展的背景脚本或者服务工作者中有效。它不能在 content scripts 或者其他的扩展页面中使用。
