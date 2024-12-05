# action

使用 chrome.action API 可控制扩展程序在 Google Chrome 工具栏中的图标。

## Manifest

```json
{
  "name": "Action Extension",
  "manifest_version": 3,
  ...
  "action": {
    "default_icon": {              // optional
      "16": "images/icon16.png",   // optional
      "24": "images/icon24.png",   // optional
      "32": "images/icon32.png"    // optional
    },
    "default_title": "Click Me",   // optional, shown in tooltip , or action.setTitle()
    "default_popup": "popup.html"  // optional
  },
  ...
}
```

## 用例

### 每个标签页的状态

每个标签页的扩展程序操作可以有不同的状态。如需为单个标签页设置值，请使用 action API 设置方法中的 tabId 属性。

如果未添加 tabId 属性，系统会将此设置视为全局设置。特定于标签页的设置优先于全局设置。

```js
function getTabId() { /* ... */}
function getTabBadge() { /* ... */}

chrome.action.setBadgeText(
  {
    text: getTabBadge(tabId),
    tabId: getTabId(),
  },
  () => { ... }
);
```

### 启用状态

默认情况下，每个标签页上的工具栏操作都处于启用状态（可点击）。您可以使用 `action.enable()` 和 `action.disable()` 方法控制此行为。这只会影响系统是否将弹出式窗口（如果有）或 `action.onClicked` 事件发送到您的扩展程序，而不会影响相应操作是否显示在工具栏中。

### 使用 declarativeContent 模拟 pageActions

此示例展示了扩展程序的后台逻辑如何 (a) 默认停用某项操作，并 (b) 使用 declarativeContent 在特定网站上启用该操作。

```js
// background.js

// Wrap in an onInstalled callback to avoid unnecessary work
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
          pageUrl: { hostSuffix: ".example.com" },
        }),
      ],
      actions: [new chrome.declarativeContent.ShowAction()],
    };

    // Finally, apply our new array of rules
    let rules = [exampleRule];
    chrome.declarativeContent.onPageChanged.addRules(rules);
  });
});
```

## Methods

### enable()、disable()

标签页启用、停用标操作。默认情况下，操作处于启用状态。

_type_

```ts
chrome.action.enable(
  tabId?: number,
  callback?: ()=>void,
);

chrome.action.disable(
  tabId?: number,
  callback?: ()=>void,
)
```

### getBadgeBackgroundColor()

获取操作的背景颜色。

_type_

```ts
chrome.action.getBadgeBackgroundColor(
  details: TabDetails,
  callback?: (result: ColorArray)=>void
);

type TabDetails = {
    tabId?: number
}
type ColorArray = [number,number,number,number]
```

### getBadgeText()

获取操作的标志文本。如果未指定制表符，则返回非制表符专用的标记文字。如果启用了 displayActionCountAsBadgeText，则系统将返回占位文本，除非具有 declarativeNetRequestFeedback 权限或提供了标签页专用的标记文本。

_type_

```ts
chrome.action.getBadgeText(
  details: TabDetails,
  callback?: (result:string)=>void,
);

type TabDetails = {
    tabId?: number
}
```

### getBadgeTextColor()

获取操作的文本颜色。

_type_

```ts
chrome.action.getBadgeTextColor(
  details:
  TabDetails,
  callback?: (result:ColorArray)=>void
);

type TabDetails = {
    tabId?: number
}
type ColorArray = [number,number,number,number]
```

### getPopup()

获取设置为此操作的弹出式窗口的 HTML 文档。

_type_

```ts
chrome.action.getPopup(
  details: TabDetails,
  callback?: (result:string)=>void,
)

type TabDetails = {
    tabId?: number
}
```

### getTitle()

获取操作的标题。

_type_

```ts
chrome.action.getTitle(
  details: TabDetails,
  callback?: (result:string)=>void,
)

type TabDetails = {
    tabId?: number
}
```

### getUserSettings()

返回与扩展程序操作相关的用户指定设置。

_type_

```ts
chrome.action.getUserSettings(
  callback?: (userSettings:UserSettings)=>void
)

type UserSettings = {
    isOnToolbar: boolean; //扩展程序的操作图标是否显示在浏览器窗口的顶层工具栏中（即用户是否已“固定”扩展程序）。
}
```

### isEnabled()

指示是否为标签页启用了扩展程序操作（或者，如果未提供 tabId，则使用全局操作）。仅使用 declarativeContent 启用的操作始终返回 false。

_type_

```ts
chrome.action.isEnabled(
  tabId?: number,
  callback?: (isEnabled: boolean)=>void,
)
```

### openPopup()

打开扩展程序的弹出式窗口。

_type_

```ts
chrome.action.openPopup(
  options?: OpenPopupOptions,
  callback?: ()=>void
)

type OpenPopupOptions = {
    windowId?: number
}
```

### setBadgeBackgroundColor()

设置标志的背景颜色。

_type_

```ts
chrome.action.setBadgeBackgroundColor(
  details: Object,
  callback?: ()=>void,
)

type Object = {
    ColorArray: string | [number, number, number, number]; //css值字符串 #FF0000 ；  [0,255] 范围内的四个整数构成的数组 [255, 0, 0, 255]
    tabId?: number;
}
```

### setBadgeText()

设置操作的标志文本。标记会显示在图标顶部。

_type_

```ts
chrome.action.setBadgeText(
  details: Object,
  callback?: ()=>void
)

type Object ={
    tabId?: number;
    text?: string; // 可以传递任意数量的字符，但空间中只能包含大约 4 个字符。如果传递了空字符串 ('')，标记文本将被清除。如果指定了 tabId 且 text 为 null，则指定标签页的文本会清除，并默认为全局标记文本。
}
```

### setBadgeTextColor()

设置标志的文字颜色。

_type_

```ts
chrome.action.setBadgeTextColor(
  details: Object,
  callback?: ()=>void,
)

type Object = {
    ColorArray: string | [number, number, number, number]; //css值字符串 #FF0000 ；  [0,255] 范围内的四个整数构成的数组 [255, 0, 0, 255]
    tabId?: number;
}
```

#### setIcon()

设置操作的图标。可将图标指定为图像文件的路径或画布元素的像素数据，或者指定这些图标的字典。必须指定 path 或 imageData 属性。

_type_

```ts
chrome.action.setIcon(
  details: Object,
  callback?: ()=>void
)

type Object = {
    imageData?: ImageData | object;
    path?: string| object;
    tabId?: number;
}
```

### setPopup()

设置 HTML 文档，使其在用户点击操作的图标时以弹出式窗口形式打开。

_type_

```ts
chrome.action.setPopup(
  details: Object,
  callback?: ()=>void,
)

type Object = {
    popup: string; //要在弹出式窗口中显示的 HTML 文件的相对路径。如果设置为空字符串 ('')，则不会显示弹出式窗口。
    tabId?: number; //将更改限定为选择特定标签页的时间。关闭标签页后自动重置。

}
```

### setTitle()

设置操作的标题。这会显示在提示中。

_type_

```ts
chrome.action.setTitle(
  details: Object,
  callback?: ()=>void
)

type Object = {
    tabId?: number;
    title: string; //鼠标悬停时操作应显示的字符串。
}
```

## Events

### onClicked

点击操作图标时触发。如果操作具有弹出式窗口，则不会触发此事件。

```ts
chrome.action.onClicked.addListener(
  callback: (tab: tabs.Tab)=>void
)
```
