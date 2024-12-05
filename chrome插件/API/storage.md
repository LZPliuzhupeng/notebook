# storage

使用 chrome.storage API 存储、检索和跟踪用户数据的更改。

## 权限

```json
{
  "name": "My extension",
  ...
  "permissions": [
    "storage"
  ],
  ...
}
```

## Methods、Events

### storage.local()

数据会存储在本地，并会在移除扩展程序时清除。存储空间上限为 10 MB（在 Chrome 113 及更早版本中为 5 MB），但您可以通过请求 "unlimitedStorage" 权限提高上限。我们建议使用 storage.local 存储大量数据。

```js
chrome.storage.local.set({ key: value }).then(() => {
  console.log("Value is set");
});

chrome.storage.local.get(["key"]).then((result) => {
  console.log("Value is " + result.key);
});
```

### storage.sync()

如果同步功能已启用，数据会同步到用户登录的任何 Chrome 浏览器。如果停用，其行为类似于 storage.local。当浏览器离线时，Chrome 会将数据存储在本地，并在浏览器恢复在线状态后恢复同步。配额限制大约为 100 KB，每项内容 8 KB。我们建议您使用 storage.sync 来保留已同步的浏览器的用户设置。如果您处理的是敏感用户数据，请改用 storage.session。

```js
chrome.storage.sync.set({ key: value }).then(() => {
  console.log("Value is set");
});

chrome.storage.sync.get(["key"]).then((result) => {
  console.log("Value is " + result.key);
});
```

### storage.session()

在浏览器会话期间将数据保留在内存中。默认情况下，它不会向内容脚本公开，但可以通过设置 `chrome.storage.session.setAccessLevel()` 来更改此行为。存储空间上限为 10 MB（在 Chrome 111 及更早版本中为 1 MB）。

```js
chrome.storage.session.set({ key: value }).then(() => {
  console.log("Value was set");
});

chrome.storage.session.get(["key"]).then((result) => {
  console.log("Value is " + result.key);
});
```

### storage.managed()

管理员可以使用架构和企业政策在受管环境中配置支持扩展程序的设置。此存储区域是只读的。

**从存储空间异步预加载**

由于 Service Worker 并非始终都会运行，因此 Manifest V3 扩展程序有时需要先从存储空间异步加载数据，然后再执行其事件处理脚本。为此，以下代码段使用异步 action.onClicked 事件处理脚本，该处理脚本会等待系统填充 storageCache 全局变量，然后再执行其逻辑。

```js
// Where we will expose all the data we retrieve from storage.sync.
const storageCache = { count: 0 };
// Asynchronously retrieve data from storage.sync, then cache it.
const initStorageCache = chrome.storage.sync.get().then((items) => {
  // Copy the data retrieved from storage into storageCache.
  Object.assign(storageCache, items);
});

chrome.action.onClicked.addListener(async (tab) => {
  try {
    await initStorageCache;
  } catch (e) {
    // Handle error that occurred during storage initialization.
  }

  // Normal action handler logic.
  storageCache.count++;
  storageCache.lastTabId = tab.id;
  chrome.storage.sync.set(storageCache);
});
```

## 事件

### onChanged

```js
chrome.storage.onChanged.addListener(
  callback:(changes:object,areaName:string)=>void,
)
```
