#### Service Worker

## 概念

Service worker 是一个注册在指定源和路径下的事件驱动worker。它采用 JavaScript 控制关联的页面或者网站，拦截并修改访问和资源请求，细粒度地缓存资源。你可以完全控制应用在特定情形（最常见的情形是网络不可用）下的表现。

Service worker 运行在 worker 上下文，因此它不能访问 DOM。相对于驱动应用的主 JavaScript 线程，它运行在其他线程中，所以不会造成阻塞。它设计为完全异步，同步 API（如XHR和localStorage (en-US)）不能在 service worker 中使用。

出于安全考量，Service workers 只能由 HTTPS 承载，毕竟修改网络请求的能力暴露给中间人攻击会非常危险。为了便于本地开发，localhost 也被浏览器认为是安全源。在 Firefox 浏览器的用户隐私模式，Service Worker 不可用。

**生命周期**

+ 下载
+ 安装
+ 激活  

用户首次访问 service worker 控制的网站或页面时，service worker 会立刻被下载。

**触发更新**

+ 一个前往作用域内页面的导航
+ 在 service worker 上的一个事件被触发并且过去 24 小时没有被下载

无论它与现有 service worker 不同（字节对比），还是第一次在页面或网站遇到 service worker，如果下载的文件是新的，安装就会尝试进行。

如果这是首次启用 service worker，页面会首先尝试安装，安装成功后它会被激活。

如果现有 service worker 已启用，新版本会在后台安装，但不会被激活，这个时序称为 worker in waiting。直到所有已加载的页面不再使用旧的 service worker 才会激活新的 service worker。只要页面不再依赖旧的 service worker，新的 service worker 会被激活（成为 active worker）。

?> 因为`oninstall`和`onactivate`完成前需要一些时间，service worker 标准提供一个`waitUntil`方法，当`oninstall`或者`onactivate`触发时被调用，接受一个 promise。在这个 promise 被成功 resolve 以前，功能性事件不会分发到 service worker。

## 指南

### 使用前的设置

在已经支持 serivce workers 的浏览器的版本中，很多特性没有默认开启。如果你发现示例代码在当前版本的浏览器中怎么样都无法正常运行，你可能需要开启一下浏览器的相关配置：

+ **Firefox Nightly**: 访问 `about:config` 并设置 `dom.serviceWorkers.enabled` 的值为 `true`; 重启浏览器；
+ **Chrome Canary**: 访问 `chrome://flags` 并开启 `experimental-web-platform-features`; 重启浏览器 (注意：有些特性在 Chrome 中没有默认开放支持)；
+ **Opera**: 访问 `opera://flags` 并开启 `ServiceWorker 的支持`; 重启浏览器。


### 基本架构

+ service worker URL 通过 `serviceWorkerContainer.register()` 来获取和注册。
+ 如果注册成功，service worker 就在 `ServiceWorkerGlobalScope` 环境中运行；这是一个特殊类型的 worker 上下文运行环境，与主运行线程（执行脚本）相独立，同时也没有访问 DOM 的能力。
+ service worker 现在可以处理事件了。
+ 受 service worker 控制的页面打开后会尝试去安装 service worker。最先发送给 service worker 的事件是安装事件 (在这个事件里可以开始进行填充 IndexDB 和缓存站点资源)。这个流程同原生 APP 或者 Firefox OS APP 是一样的 — 让所有资源可离线访问。
+ 当 `oninstall` 事件的处理程序执行完毕后，可以认为 service worker 安装完成了。
+ 下一步是激活。当 service worker 安装完成后，会接收到一个激活事件 (activate event)。 `onactivate` 主要用途是清理先前版本的 service worker 脚本中使用的资源。
+ Service Worker 现在可以控制页面了，但仅是在 `register()` 成功后的打开的页面。也就是说，页面起始于有没有 service worker，且在页面的接下来生命周期内维持这个状态。所以，页面不得不重新加载以让 service worker 获得完全的控制。


### 注册

```js
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw-test/sw.js', { scope: '/sw-test/' }).then(function(reg) {
    // registration worked
    console.log('Registration succeeded. Scope is ' + reg.scope);
  }).catch(function(error) {
    // registration failed
    console.log('Registration failed with ' + error);
  });
}
```

1、外面的代码块做了一个特性检查，在注册之前确保 service worker 是支持的。

2、接着，我们使用 `ServiceWorkerContainer.register()` 函数来注册站点的 service worker，service worker 只是一个驻留在我们的 app 内的一个 JavaScript 文件 (注意，这个文件的 url 是相对于 origin，而不是相对于引用它的那个 JS 文件)。

3、`scope` 参数是选填的，可以被用来指定你想让 service worker 控制的内容的子目录。在这个例子里，我们指定了 `'/sw-test/'`，表示 app 的 origin 下的所有内容。如果你留空的话，默认值也是这个值，我们在指定只是作为例子。

单个 service worker 可以控制很多页面。每个你的 scope 里的页面加载完的时候，安装在页面的 service worker 可以控制它。 service worker 脚本里的全局变量：每个页面不会有自己独有的 worker。


**service worker 注册失败了？**

+ 你没有在 HTTPS 下运行你的程序
+ service worker 文件的地址没有写对— 需要相对于 origin , 而不是 app 的根目录。在我们的例子例，service worker 是在 https://mdn.github.io/sw-test/sw.js，app 的根目录是 https://mdn.github.io/sw-test/。应该写成 /sw-test/sw.js 而非 /sw.js.
+ service worker 在不同的 origin 而不是你的 app 的，这是不被允许的。

注意：
+ service worker 只能抓取在 service worker scope 里从客户端发出的请求。
+ 最大的 scope 是 service worker 所在的地址
+ 如果你的 service worker 被激活在一个有 Service-Worker-Allowed header 的客户端，你可以为 service worker 指定一个最大的 scope 的列表。
+ 在 Firefox, Service Worker APIs 在用户在 private browsing mode 下会被隐藏而且无法使用。


### 安装和激活：填充你的缓存

在你的 service worker 注册之后，浏览器会尝试为你的页面或站点安装并激活它。

`install` 事件会在注册完成之后触发。`install` 事件一般是被用来填充你的浏览器的离线缓存能力。

为了达成这个目的，我们使用了 Service Worker 的新的标志性的存储 API — `cache` — 一个 service worker 上的全局对象，它使我们可以存储网络响应发来的资源，并且根据它们的请求来生成 key。这个 API 和浏览器的标准的缓存工作原理很相似，但是是特定你的域的。

它会一直持久存在，直到你告诉它不再存储，你拥有全部的控制权。

!> 备注： Cache API 并不被每个浏览器支持。

```js
this.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v1').then(function(cache) {
      return cache.addAll([
        '/sw-test/',
        '/sw-test/index.html',
        '/sw-test/style.css',
        '/sw-test/app.js',
        '/sw-test/image-list.js',
        '/sw-test/star-wars-logo.jpg',
        '/sw-test/gallery/',
        '/sw-test/gallery/bountyHunters.jpg',
        '/sw-test/gallery/myLittleVader.jpg',
        '/sw-test/gallery/snowTroopers.jpg'
      ]);
    })
  );
});
```

1、这里我们 新增了一个 `install` 事件监听器，接着在事件上接了一个`ExtendableEvent.waitUntil()` 方法——这会确保 Service Worker 不会在 `waitUntil()` 里面的代码执行完毕之前安装完成。
2、在 `waitUntil()` 内，我们使用了 `caches.open()` 方法来创建了一个叫做 v1 的新的缓存，将会是我们的站点资源缓存的第一个版本。它返回了一个创建缓存的 promise，当它 resolved 的时候，我们接着会调用在创建的缓存示例上的一个方法 addAll()，这个方法的参数是一个由一组相对于 origin 的 URL 组成的数组，这些 URL 就是你想缓存的资源的列表。
3、如果 promise 被 rejected，安装就会失败，这个 worker 不会做任何事情。这也是可以的，因为你可以修复你的代码，在下次注册发生的时候，又可以进行尝试。
4、当安装成功完成之后，service worker 就会激活。在第一次你的 service worker 注册／激活时，这并不会有什么不同。但是当 service worker 更新的时候，就不太一样了。

!> 1、localStorage (en-US) 跟 service worker 的 cache 工作原理很类似，但是它是同步的，所以不允许在 service workers 内使用。   
2、IndexedDB 可以在 service worker 内做数据存储。


### 自定义请求的响应

每次任何被 service worker 控制的资源被请求到时，都会触发 `fetch` 事件，这些资源包括了指定的 scope 内的文档，和这些文档内引用的其他任何资源（比如 index.html 发起了一个跨域的请求来嵌入一个图片，这个也会通过 service worker。）

你可以给 service worker 添加一个 `fetch` 的事件监听器，接着调用 event 上的 `respondWith()` 方法来劫持我们的 HTTP 响应，然后你用可以用自己的方法来更新他们。
```js
this.addEventListener('fetch', function(event) {
  event.respondWith(
    // magic goes here
  );
});
```

caches.match(event.request) 允许我们对网络请求的资源和 cache 里可获取的资源进行匹配，查看是否缓存中有相应的资源。这个匹配通过 url 和 vary header 进行，就像正常的 http 请求一样。
```js
this.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
  );
});
```

1、`Response()` 构造函数允许你创建一个自定义的 response。在这个例子中，我们只返回一个示例的字符串：
```js
new Response('Hello from your friendly neighbourhood service worker!');
```

2、下面这个更复杂点的 `Response` 展示了你可以在你的响应里选择性的传一系列 header，来模仿标准的 HTTP 响应 header。这里我们只告诉浏览器我们虚假的响应的 content type：
```js
new Response('<p>Hello from your friendly neighbourhood service worker!</p>', {
  headers: { 'Content-Type': 'text/html' }
})
```

3、如果没有在缓存中找到匹配的资源，你可以告诉浏览器对着资源直接去 `fetch` 默认的网络请求：
```js
fetch(event.request)
```

4、如果没有在缓存中找到匹配的资源，同时网络也不可用，你可以用 `match()` 把一些回退的页面作为响应来匹配这些资源，比如：
```js
caches.match('/fallback.html');
```
5、你可以通过 `FetchEvent` 返回的 `Request` 对象检索到非常多有关请求的信息：
+ event.request.url
+ event.request.method
+ event.request.headers
+ event.request.body


### 恢复失败的请求 (不懂)

在有 service worker cache 里匹配的资源时， `caches.match(event.request)` 是非常棒的。但是如果没有匹配资源呢？如果我们不提供任何错误处理，promise 就会 reject，同时也会出现一个网络错误。
```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request);
    })
  );
});
```

不只是从服务器请求资源，而且还会把请求到的资源保存到缓存中，以便将来离线时所用！这意味着如果其他额外的图片被加入到 Star Wars 图库里，我们的 app 会自动抓取它们。
```js
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(resp) {
      return resp || fetch(event.request).then(function(response) {
        return caches.open('v1').then(function(cache) {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    })
  );
});
```

这里我们用 `fetch(event.request)` 返回了默认的网络请求，它返回了一个 promise。

当网络请求的 promise 成功的时候，我们 通过执行一个函数用 `caches.open('v1')` 来抓取我们的缓存，它也返回了一个 promise。

当这个 promise 成功的时候， `cache.put()` 被用来把这些资源加入缓存中。资源是从 `event.request` 抓取的，它的响应会被 `response.clone()` 


现在唯一的问题是当请求没有匹配到缓存中的任何资源的时候，以及网络不可用的时候，我们的请求依然会失败。让我们提供一个默认的回退方案以便不管发生了什么，用户至少能得到些东西：
```js
this.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
    .then(function() {
      return fetch(event.request).then(function(response) {
        return caches.open('v1').then(function(cache) {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    })
    .catch(function() {
      return caches.match('/sw-test/gallery/myLittleVader.jpg');
    })
  );
});
```


### 更新你的 service worker

如果你的 service worker 已经被安装，但是刷新页面时有一个新版本的可用，新版的 service worker 会在后台安装，但是还没激活。当不再有任何已加载的页面在使用旧版的 service worker 的时候，新版本才会激活。一旦再也没有更多的这样已加载的页面，新的 service worker 就会被激活。

把你的新版的 service worker 里的 install 事件监听器改成下面这样（注意新的版本号）：
```js
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v2').then(function(cache) {
      return cache.addAll([
        '/sw-test/',
        '/sw-test/index.html',
        '/sw-test/style.css',
        '/sw-test/app.js',
        '/sw-test/image-list.js',

        …

        // include other new resources for the new version...
      ]);
    })
  );
});
```


### 删除旧缓存

你还有个 `activate` 事件。当之前版本还在运行的时候，一般被用来做些会破坏它的事情，比如摆脱旧版的缓存。在避免占满太多磁盘空间清理一些不再需要的数据的时候也是非常有用的，每个浏览器都对 service worker 可以用的缓存空间有个硬性的限制。浏览器尽力管理磁盘空间，但它可能会删除整个域的缓存。浏览器通常会删除域下面的所有的数据。

传给 `waitUntil()` 的 promise 会阻塞其他的事件，直到它完成。所以你可以确保你的清理操作会在你的的第一次 fetch 事件之前会完成。
```js
self.addEventListener('activate', function(event) {
  var cacheWhitelist = ['v2'];

  event.waitUntil(
    caches.keys().then(function(keyList) {
      return Promise.all(keyList.map(function(key) {
        if (cacheWhitelist.indexOf(key) === -1) {
          return caches.delete(key);
        }
      }));
    })
  );
});
```


## 开发者工具

Chrome 
+ chrome://inspect/#service-workers 可以展示当前设备上激活和存储的 service worker。
+ chrome://serviceworker-internals 可以展示更多细节来允许你开始/暂停/调试 worker 的进程。

Firefox
+ about:serviceworkers 查看注册了什么 SW，可以更新和移除他们。
+ 当测试时你想绕开 HTTPS 限制时，可以检查 Firefox Devtools 的选项 "Enable Service Workers over HTTP (when toolbox is open)" （齿轮图标）