#### manifest说明<r>（V3）</r>

[Chrome Developers官方文档](https://developer.chrome.com/docs/extensions/mv3/manifest/)

## manifest_version

固定值`3`，`2`官方已不推荐
```js
"manifest_version": 3
```
![Manifest V2 support timeline](../assets/image/chrome插件/support-timeline.avif)



## name
插件正式名称（maximum of 45 characters）



## version
自定义版本号

```js
"version": "1"
"version": "1.0"
"version": "2.10.2"
"version": "3.1.2.4567"
```


## action
Chrome扩展菜单(拼图块) 或 工具栏图标

```js
{
  "name": "Action Extension",
  ...
  "action": {
    "default_icon": {              // optional
      "16": "images/icon16.png",   // optional
      "24": "images/icon24.png",   // optional
      "32": "images/icon32.png"    // optional
    },
    "default_icon": "images/icon.png", // optional
    "default_title": "Click Me",   // optional, shown in tooltip
    "default_popup": "popup.html"  // optional
  },
  ...
}
```

### default_icon
插件图标，可设置多个尺寸。

图标是16个DIPs(设备独立像素)宽和高。图标最初是由清单中的操作条目中的default_icon键设置的。Chrome将使用这些图标来选择使用哪个图像比例。如果没有找到精确的匹配，Chrome将选择最接近的可用和缩放它以适应图像。然而，这种缩放会导致图标失去细节或看起来模糊。

### default_title
插件图标悬浮的提示或标题，默认为manifest.name

### default_popup
点击插件图标的popup弹窗

### *Badge*
*不为manifest清单字段*

此为action上右下角的一个‘徽章’, 可设置文本与背景色。

+ setBadgeBackgroundColor()
+ setBadgeText()

```js
chrome.action.setBadgeBackgroundColor(
  {color: [0, 255, 0, 0]},  // Green
  () => { /* ... */ },
);

chrome.action.setBadgeBackgroundColor(
  {color: '#00FF00'},  // Also green
  () => { /* ... */ },
);

chrome.action.setBadgeBackgroundColor(
  {color: 'green'},  // Also, also green
  () => { /* ... */ },
);
```

### default_popup
此为个html页面，可自定义尺寸最小25x25，最大800x600。

在action.default_popup 设置相对路径。

也可action.setPopup()动态设置相对路径来设置

+ setPopup()
+ onClicked.addListener()

```js
// background.js
chrome.action.onClicked.addListener((tab) => {
  
})

```
?> 如果设置了default_popup，action.onClicked不会被调用


### *Enabled state*
By default, toolbar actions are enabled (clickable) on every tab. You can control this using the action.enable() and action.disable() methods. This only affects whether the popup (if any) or action.onClicked event is dispatched to your extension; it does not affect the action's presence in the toolbar.


### *declarativeContent*

**使用declarativeContent模拟pageActions -- `代码未生成/未知原因*`*

这个例子展示了一个扩展的后台逻辑是如何(a)默认禁用该操作，(b)使用declarativeContent在特定站点上启用该操作的。

```js
// background.js

// Wrap in an onInstalled callback in order to avoid unnecessary work
// every time the background script is run
chrome.runtime.onInstalled.addListener(() => {
    // Page actions are disabled by default and enabled on select tabs
    chrome.action.disable();
  
    // Clear all rules to ensure only our expected rules are set
    chrome.declarativeContent.onPageChanged.removeRules(undefined, () => {
      // Declare a rule to enable the action on example.com pages
      let exampleRule = {
        conditions: [
          new chrome.declarativeContent.PageStateMatcher({
            pageUrl: {hostSuffix: '*.facebook.com'},
          })
        ],
        actions: [new chrome.declarativeContent.ShowAction()],
      };
  
      // Finally, apply our new array of rules
      let rules = [exampleRule];
      chrome.declarativeContent.onPageChanged.addRules(rules);
    });
  }); 
```

#### Types
```ts
type OpenPopupOptions = {
  windowId:Number,
}
type TabDetails = {
  tabId:Number,
}
type UserSettings = {
  isOnToolbar:Boolean, // 扩展的操作图标是否可见在浏览器窗口的顶层工具栏(即，是否扩展已被用户“钉住”)。
}
```

### *API*



+ `enable()`
<details>
  <summary>展开</summary>
  
  ```js
    chrome.action.enable(
      tabId?: number,
      callback?: function,
    )
  ```
</details>

+ `disable()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.disable(
        tabId?: number,
        callback?: function,
    )
  ```
</details>

+ `getBadgeBackgroundColor()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.getBadgeBackgroundColor(
      details: TabDetails,
      callback?: function,
    )
  ```
</details>

+ `getBadgeText()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.getBadgeText(
      details: TabDetails,
      callback?: function,
    )
  ```
</details>

+ `getPopup()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.getPopup(
      details: TabDetails,
      callback?: function,
    )
  ```
</details>

+ `getTitle()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.getTitle(
      details: TabDetails,
      callback?: function,
    )
  ```
</details>

+ `getUserSettings()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.getUserSettings(
      callback?: function, // (userSettings: UserSettings) => void
    )
  ```
</details>

+ `openPopup()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.openPopup(
      options?: OpenPopupOptions,
      callback?: function,
    )
  ```
</details>

+ `setBadgeBackgroundColor()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.setBadgeBackgroundColor(
      details: object, // { color: String | ColorArray, tabId?: Number } , { ColorArray: [number, number, number, number] }
      callback?: function,
    )
  ```
</details>

+ `setBadgeText()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.setBadgeText(
      details: object, // { tabId?: Number, text: String }
      callback?: function,
    )
  ```
</details>

+ `setIcon()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.setIcon(
      details: object, // { imageData?: ImageData | object, path?: string | object, tabId?: Number }
      callback?: function,
    )
  ```
</details>

+ `setPopup()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.setPopup(
      details: object, // { popup: String（HTML文件的相对路径 ）, tabId?: Number }
      callback?: function,
    )
  ```
</details>

+ `setTitle()`
<details>
  <summary>展开</summary>

  ```js
    chrome.action.setTitle(
      details: object, // { tabId?: Number, title: String }
      callback?: function,
    )
  ```
</details>



### *Events*

+ `onClicked`    
当单击操作图标时触发。如果动作有一个弹出窗口，这个事件将不会触发。
<details>
  <summary>展开</summary>

  ```js
    chrome.action.onClicked.addListener(
      callback: function, // (tab: tabs.Tab) => void
    )
  ```
</details>



## background 

```json
 "background": {
    "service_worker": "background.js",
    "type": ...
  },
```

?> 用于"service_worker"的脚本必须位于扩展的根目录中。

你可以选择指定一个额外的字段"type": "module"来将service worker包含为ES module，这允许你导入更多的代码。更多信息请参见[service worker中的ES模块](https://web.dev/es-modules-in-sw/)。例如:
```json
  "background": {
    "service_worker": "background.js",
    "type": "module"
  }
```

#### Initialize the extension

监听运行时。onInstalled事件在安装时初始化扩展。使用此事件可设置状态或进行一次性初始化，例如上下文菜单。
```js
chrome.runtime.onInstalled.addListener(() => {
  chrome.contextMenus.create({
    "id": "sampleContextMenu",
    "title": "Sample Context Menu",
    "contexts": ["selection"]
  });
});
```

侦听器必须从页面的开始同步注册。
```js
chrome.runtime.onInstalled.addListener(() => {
  chrome.contextMenus.create({
    "id": "sampleContextMenu",
    "title": "Sample Context Menu",
    "contexts": ["selection"],
  });
});

// This will run when a bookmark is created.
chrome.bookmarks.onCreated.addListener(() => {
  // do something
});
```


不要异步注册侦听器，因为它们不会被正确触发。
```js
chrome.runtime.onInstalled.addListener(() => {
  // ERROR! Events must be registered synchronously from the start of
  // the page.
  chrome.bookmarks.onCreated.addListener(() => {
    // do something
  });
});
```

扩展可以通过调用removelistener从其后台脚本中删除侦听器。如果一个事件的所有监听器被删除，Chrome将不再加载该事件的扩展的后台脚本。
```js
chrome.runtime.onMessage.addListener((message, sender, reply) => {
  chrome.runtime.onMessage.removeListener(event);
});
```

#### 过滤事件`(不懂)`

使用支持事件过滤器的apl将监听器限制到扩展所关心的情况。如果一个扩展正在监听标签。onUpdated事件，尝试使用webNavigation。使用过滤器代替onCompleted事件，因为标签API不支持过滤器。
```js
const filter = {
  url: [
    {
      urlMatches: 'https://www.google.com/',
    },
  ],
};

chrome.webNavigation.onCompleted.addListener(() => {
  console.info("The user has loaded my favorite website!");
}, filter);
```


## content_scripts

内容脚本是在网页环境中运行的文件。通过使用标准的文档对象模型(DOM)，它们能够读取浏览器访问的网页的详细信息，对它们进行更改，并将信息传递给它们的父扩展。

不仅每个扩展都在自己独立的世界中运行，而且内容脚本和网页也是如此。这意味着这些(网页、内容脚本和任何正在运行的扩展)都不能访问其他程序的上下文和变量。

#### 了解 content script

内容脚本可以通过与扩展交换消息来访问其父扩展所使用的Chrome apl。也可以访问URL扩展的文件与chrome。

runtime.getURL()并使用与其他url相同的结果。
```js
// Code for displaying EXTENSION_DIR/images/myimage.png:
var imgURL = chrome.runtime.getURL("images/myimage.png");
document.getElementById("someImage").src = imgURL;
```

另外，内容脚本可以直接访问以下chrome api:

+ i18n
+ storage
+ runtime:
  - connect
  - getManifest
  - getURL
  - id
  - onConnect
  - onMessage
  - sendMessage


内容脚本无法直接访问其他api。


### *Inject scripts*
内容脚本可以静态声明，也可以以编程方式注入。

#### 使用静态声明注入

```json
{
 "name": "My extension",
 ...
 "content_scripts": [
   {
     "matches": ["https://*.nytimes.com/*"],
     "css": ["my-styles.css"],
     "js": ["content-script.js"],

     ...
     "run_at":"",
   }
 ],
 ...
}
```

<div class="manifest__inject-scripts__table">

Name |	Type |	Description
:--- | :----: | ---
matches |	array of strings |	必需的。<br/>指定此内容脚本将注入到哪些页面。有关这些字符串语法的[更多Match Patterns](/chrome插件/manifest说明?id=match-patterns)，以及关于如何排除url，[更多Match patterns and globs](/chrome插件/manifest说明id=match-patterns-and-globs)。
css |	array of strings |	可选的。<br/>要注入到匹配页面中的CSS文件列表。在页面构造或显示DOM之前，按照它们在数组中出现的顺序注入。
js |	array of string |	可选的。<br/>要注入到匹配页面中的JavaScript文件列表。它们是按照它们在数组中出现的顺序注入的。
match_about_blank |	boolean |	Optional. Whether the script should inject into an about:blank frame where the parent or opener frame matches one of the patterns declared in matches. Defaults to false.
match_origin_as_fallback |	boolean |	Optional. Whether the script should inject in frames that were created by a matching origin, but whose URL or origin may not directly match the pattern. These include frames with different schemes, such as about:, data:, blob:, and filesystem:. See also Injecting in related frames.

</div>

<style>
.manifest__inject-scripts__table tr th{
  width:20%
}
.manifest__inject-scripts__table tr th:last-of-type{
  width: 60%;
}
</style>


### *Match Patterns*

主机权限和内容脚本匹配基于匹配模式定义的一组url。匹配模式本质上是一个URL，它以允许的方案(http、https、file或ftp)开始，可以包含'*'字符。特殊模式<all_urls>匹配以允许的模式开始的任何URL。每个匹配模式由3部分组成:

+ scheme(协议) — for example, `http` or `file` or `*`

!> 对文件url的访问不是自动的。用户必须访问扩展管理页面，并选择对每个请求它的扩展进行文件访问。

+ host(主机) — for example, `www.google.com or` `*.google.com` or `*`; if the scheme is file, there is no host part

+ path(路径) — for example, `/*`, `/foo*`, or `/foo/bar`. The path must be present in a host permission, but is always treated as `/*`.


基本语法：
```js
<url-pattern> := <scheme>://<host><path>
<scheme> := '*' | 'http' | 'https' | 'file' | 'ftp' | 'urn'
<host> := '*' | '*.' <any char except '/' and '*'>+
<path> := '/' <any chars>
```

'`*`'的含义取决于它是在`scheme`、`host`还是`path`部分。

如果scheme是`*`，那么它匹配`http`或`https`，而**不是**`file`、`ftp`或`urn`。

如果host只是`*`，那么它匹配任何主机。主机为`*._hostname_`，则它匹配指定的主机或其任何子域。

在path部分，每个'`*`'匹配0个或多个字符。


Pattern |	What it does |	Examples of matching URLs
:--- | :----: | ---
`https://*/*` |	匹配任何使用`https`协议的URL |	https\://www\.google\.com/ <br/> https\://example\.org/foo/bar.html
`https://*/foo*` |	匹配任何使用`https`协议、路径以`/foo`开头的URL |	https\://example\.com/foo/bar.html <br/> https\://www\.google.com/foo
`https://*.google.com/foo*bar` |	匹配任何使用`https`协议、是在google.com主机上 <br/> (如www\.google.com, docs\.google.com，或google.com)、路径以`/foo`开始、以`bar`结束 的URL |	https\://www\.google.com/foo/baz/bar <br/> https\://docs\.google.com/foobar
`https://example.org/foo/bar.html` |	匹配指定的URL |	https\://example\.org/foo/bar.html
`file:///foo*` |	匹配任何路径以`/foo`开头的本地文件 |	file:///foo/bar.html <br/> file:///foo
`http://127.0.0.1/*` |	匹配主机127.0.0.1上使用`http`协议的任何URL |	http\://127.0\.0.1/ <br/> http\://127.0.0.1/foo/bar.html
`*://mail.google.com/*` |	匹配以`http://mail.google.com`开头或`https://mail.google.com` 的任何URL | 	http\://mail\.google.com/foo/baz/bar <br/> https\://mail\.google.com/foobar
`urn:*` |	匹配任何以`urn:`开头的URL |	urn:uuid:54723bea-c94e-480e-80c8-a69846c3f582 <br/> urn:uuid:cfa40aff-07df-45b2-9f95-e023bcf4a6da
`<all_urls>` |	匹配的任何URL使用允许的方案。(See the beginning of this section for the list of permitted schemes.) |	http\://example.org/foo/bar.html <br/> file:///bar/baz.html

无效模式匹配:

Bad pattern |	Why it's bad
--- | --- 
`https://www.google.com` |	没有路径
`https://*foo/bar` |	'*' 在主机中只能后跟一个'.”或“/”
`https://foo.*.bar/baz` | 	如果'*'在主机中，它必须是第一个字符
`http:/bar` |	缺少方案分隔符(“/”应该是“//”)
`foo://*` |	无效的协议



### *Match patterns and globs*

Name |	Type |	Description
--- | --- | ---
exclude_matches |	array of strings |	可选的。 <br/> 排除本内容脚本将被注入的页面。[更多Match Patterns](/chrome插件/manifest说明?id=match-patterns)
include_globs |	array of strings |	可选的。应用于匹配后，仅包括那些也匹配此glob的url。用于模拟`@include` Greasemonkey关键字。
exclude_globs |	array of string |	可选的。应用于匹配之后，以排除匹配此集合的url。用于模拟`@exclude` Greasemonkey关键字。


如果以下两个条件都满足，内容脚本将被注入到页面中:
+ 它的URL匹配任何匹配`matches`和任何`include_globs`模式
+ URL也不匹配`exclude_matches`或`exclude_globs`模式。因为`matches`属性是必需的，所以`exclude_matches`、`include_globs`和`exclude_globs`只能用于限制哪些页面会受到影响。


下面的扩展将内容脚本注入**https\://www\.nytimes.com/health**，但不注入**https\://www\.nytimes.com/business**。
```json
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["https://*.nytimes.com/*"],
      "exclude_matches": ["*://*/*business*"],
      "js": ["contentScript.js"]
    }
  ],
  ...
}
```
```js
chrome.scripting.registerContentScript({
  id: 1,
  matches: ["https://*.nytimes.com/*"],
  exclude_matches: ["*://*/*business*"],
  js: ["contentScript.js"]
});
```

与匹配模式相比，Glob属性遵循**不同的、更灵活的**语法。可接受的通配符字符串是可以包含“通配符”星号和问号的url。星号`*`匹配任何长度的字符串，包括空字符串，而问号`?`匹配任何单个字符。

例如，https\://???\.example.com/foo/* 

glob匹配以下任何一个:
+ https\://www\.example.com/foo/bar
+ https\://the\.example.com/foo/

不匹配:
+ https\://my\.example.com/foo/bar
+ https\://example\.com/foo/
+ https\://www\.example.com/foo


这个扩展将内容脚本注入**https\://www\.nytimes.com/arts/index.html**和**https\://www\.nytimes.com/jobs/index.html**，但不注入**https\://www\.nytimes.com/sports/index.html**:
```json
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["https://*.nytimes.com/*"],
      "include_globs": ["*nytimes.com/???s/*"],
      "js": ["contentScript.js"]
    }
  ],
  ...
}
```
```js
chrome.scripting.registerContentScript({
  id: 1,
  matches: ['https://*.nytimes.com/*'],
  include_globs: ['*nytimes.com/???s/*'],
  js: ['contentScript.js']
});
```


这个扩展将内容脚本注入**https\://history\.nytimes.com**和**https\://\.nytimes.com/history**，但不进入**https\://science\.nytimes.com**或**https\://www\.nytimes.com/science**:
```json
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["https://*.nytimes.com/*"],
      "exclude_globs": ["*science*"],
      "js": ["contentScript.js"]
    }
  ],
  ...
}
```
```js
chrome.scripting.registerContentScript({
  id: 1,
  matches: ['https://*.nytimes.com/*'],
  exclude_globs: ['*science*'],
  js: ['contentScript.js']
});
```


可以包含其中的一个、全部或部分来实现正确的范围。
```json
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["https://*.nytimes.com/*"],
      "exclude_matches": ["*://*/*business*"],
      "include_globs": ["*nytimes.com/???s/*"],
      "exclude_globs": ["*science*"],
      "js": ["contentScript.js"]
    }
  ],
  ...
}
```
```js
chrome.scripting.registerContentScript({
  matches: ['https://*.nytimes.com/*'],
  exclude_matches: ['*://*/*business*'],
  include_globs: ['*nytimes.com/???s/*'],
  exclude_globs: ['*science*'],
  js: ['contentScript.js']
});
```


### *Run time*(run_at)

`run_at`字段控制何时将JavaScript文件注入到web页面。首选值和默认值是`"document_idle"`，但如果需要，你也可以指定`"document_start"`或`"document_end"`。

```json
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["https://*.nytimes.com/*"],
      "run_at": "document_idle",
      "js": ["contentScript.js"]
    }
  ],
  ...
}
```
```js
chrome.scripting.registerContentScript({
  matches: ['https://*.nytimes.com/*'],
  run_at: 'document_idle',
  js: ['contentScript.js']
});
```

Name |	Type |	Description
--- | --- | ---
`document_idle` |	string |	优先。尽可能使用`document_idle`。<br/><br/>浏览器选择在`document_end`和`window.onload`事件触发之后之间的时间注入脚本。注入的确切时间取决于文档的复杂程度和加载所需的时间，并且针对页面加载速度进行了优化 <br/><br/>使用`document_idle`注入脚本不需要监听`window.onload`事件，它们保证在DOM完成后运行。如果一个脚本确实需要在`window.onload`事件之后运行。扩展可以通过`document.readyState`来判断`onload`是否已经启用。
`document_start` |	string |	脚本注入在任何来自css的文件之后，但在任何其他DOM构造或任何其他脚本运行之前。
`document_end` |	string |	脚本会在DOM完成后立即注入，但在加载图像和框架等子资源之前。


### *Specify frames*(all_frames)

`"all_frames"`字段允许扩展名指定JavaScript和CSS文件是应该注入到所有匹配指定URL要求的帧中，还是只注入到标签页中最上面的帧中。
```json
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["https://*.nytimes.com/*"],
      "all_frames": true,
      "js": ["contentScript.js"]
    }
  ],
  ...
}
```
```js
chrome.scripting.registerContentScript({
  matches: ['https://*.nytimes.com/*'],
  all_frames: true,
  js: ['contentScript.js']
});
```

Name |	Type |	Description
--- | --- | ---
`all_frames` |	boolean |	可选的. 默认为`false`, 这意味着只匹配顶部帧。<br/><br/>如果指定`true`, 如果指定为true，它将注入到所有帧中，即使该帧不是选项卡中最上面的帧。每一帧都独立检查URL需求，如果URL需求不满足，它不会注入到子帧中。


### *Injecting in related frames*（match_origin_as_fallback）`(不懂)` <y>待完善</y>

相关帧的注入

扩展可能希望在与匹配的帧相关，但本身不匹配的帧中运行脚本。这种情况的常见情况是，带有URL的帧由匹配的帧创建，但其URL本身与脚本指定的模式不匹配。


### Communication with the embedding page

与嵌入页面的通信

虽然内容脚本的执行环境和承载它们的页面彼此隔离，但它们共享对页面DOM的访问。如果页面希望通过内容脚本与内容脚本通信，或者通过内容脚本与扩展通信，那么它必须通过共享DOM进行通信。

**`window.postMessage`**
```js
var port = chrome.runtime.connect();

window.addEventListener("message", (event) => {
  // We only accept messages from ourselves
  if (event.source != window) {
    return;
  }

  if (event.data.type && (event.data.type == "FROM_PAGE")) {
    console.log("Content script received: " + event.data.text);
    port.postMessage(event.data.text);
  }
}, false);
```
```js
document.getElementById("theButton").addEventListener("click", () => {
  window.postMessage({ type: "FROM_PAGE", text: "Hello from the webpage!" }, "*");
}, false);
```


### Stay secure

确保过滤恶意网页。例如，以下模式是危险的，在Manifest V3中是不允许的:

**<r>Don't</r>**
```js
const data = document.getElementById("json-data")
// WARNING! Might be evaluating an evil script!
const parsed = eval("(" + data + ")")
```
```js
const elmt_id = ...
// WARNING! elmt_id might be "); ... evil script ... //"!
window.setTimeout("animate(" + elmt_id + ")", 200);
```


**<g>Do</g>**
```js
const data = document.getElementById("json-data")
// JSON.parse does not evaluate the attacker's scripts.
const parsed = JSON.parse(data);
```
```js
const elmt_id = ...
// The closure form of setTimeout does not evaluate scripts.
window.setTimeout(() => animate(elmt_id), 200);
```