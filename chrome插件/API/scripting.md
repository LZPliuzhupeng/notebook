# scripting

使用 chrome.scripting API 将 JavaScript 和 CSS 注入网站。这与使用内容脚本执行的操作类似。

## 权限

_manifest.json_

```json
{
  "name": "My extension",
  ...
  "permissions": [
    "scripting"
  ],
  ...
}
```

## Methods

### executeScript()

_type_

```ts
chrome.scripting.executeScript(
  injection: ScriptInjection,
  callback?: function,
)

type ScriptInjection = {
    args?: any[]; //要传递给所提供函数的参数。仅当指定了 func 参数时，此字段才有效。这些参数必须可进行 JSON 序列化。
    files?: string[]; //要注入的 JS 或 CSS 文件的路径（相对于扩展程序的根目录）。必须指定 files 和 func 中的一个。
    injectImmediately?: boolean;
    target?: InjectionTarget;//用于指定要将脚本注入到的目标的详细信息。
    world?: "ISOLATED" | "world",
    func?: ()=> {...}
}
type InjectionTarget = {
  allFrames?: boolean; //是否将脚本注入到标签页中的所有框架中。默认值为 false。如果指定了 frameIds，则此值不得为 true。
  documentIds?: string[]; //要注入的特定 documentId 的 ID。如果设置了 frameIds，则不得设置此字段。
  frameIds?: number[]; //要注入到的特定帧的 ID。
  tabId: number; //要注入的标签页的 ID。 必传
};
```

**文件**

```js
function getTabId() { ... }

chrome.scripting
    .executeScript({
      target : {tabId : getTabId()},
      files : [ "script.js" ],
    })
    .then(() => console.log("script injected"));
```

**运行时函数**

```js
function getTabId() { ... }
function getTitle() { return document.title; }

chrome.scripting
    .executeScript({
      target : {tabId : getTabId()},
      func : getTitle,
    })
    .then(() => console.log("injected a function"));
```

**args**

```js
function getTabId() { ... }
function getUserColor() { ... }
function changeBackgroundColor(backgroundColor) {
  document.body.style.backgroundColor = backgroundColor;
}

chrome.scripting
    .executeScript({
      target : {tabId : getTabId()},
      func : changeBackgroundColor,
      args : [ getUserColor() ],
    })
    .then(() => console.log("injected a function"));
```

**promise**
如果脚本执行的结果值是一个 promise，则 Chrome 会等待该 promise 产生结果并返回结果值。

```js
function getTabId() { ... }
async function addIframe() {
  const iframe = document.createElement("iframe");
  const loadComplete =
      new Promise(resolve => iframe.addEventListener("load", resolve));
  iframe.src = "https://example.com";
  document.body.appendChild(iframe);
  await loadComplete;
  return iframe.contentWindow.document.title;
}

chrome.scripting
    .executeScript({
      target : {tabId : getTabId(), allFrames : true},
      func : addIframe,
    })
    .then(injectionResults => {
      for (const frameResult of injectionResults) {
        const {frameId, result} = frameResult;
        console.log(`Frame ${frameId} result:`, result);
      }
    });
```

**取消注册所有动态内容脚本**

```js
async function unregisterAllDynamicContentScripts() {
  try {
    const scripts = await chrome.scripting.getRegisteredContentScripts();
    const scriptIds = scripts.map((script) => script.id);
    return chrome.scripting.unregisterContentScripts(scriptIds);
  } catch (error) {
    const message = [
      "An unexpected error occurred while",
      "unregistering dynamic content scripts.",
    ].join(" ");
    throw new Error(message, { cause: error });
  }
}
```

### insertCSS()

_type_

```ts
chrome.scripting.insertCSS(
  injection:
  CSSInjection,
  callback?:
  function,
)

type CSSInjection = {
    css?: string;
    files?: string[],
    origin?: "AUTHOR" | "USER";
    target: InjectionTarget;

}
type InjectionTarget = {
  allFrames?: boolean; //是否将脚本注入到标签页中的所有框架中。默认值为 false。如果指定了 frameIds，则此值不得为 true。
  documentIds?: string[]; //要注入的特定 documentId 的 ID。如果设置了 frameIds，则不得设置此字段。
  frameIds?: number[]; //要注入到的特定帧的 ID。
  tabId: number; //要注入的标签页的 ID。 必传
};
```

**运行时字符串**

```js
function getTabId() { ... }
const css = "body { background-color: red; }";

chrome.scripting
    .insertCSS({
      target : {tabId : getTabId()},
      css : css,
    })
    .then(() => console.log("CSS injected"));
```
