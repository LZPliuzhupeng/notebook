# UIAbility

## 生命周期

### Create

_在 UIAbility 实例创建时触发_

可以在 `onCreate` 回调中进行相关初始化操作

### WindowStageCreate

_WindowStage 为本地窗口管理器，用于管理窗口相关的内容，例如与界面相关的获焦/失焦、可见/不可见。_

UIAbility 实例创建完成之后，在进入 Foreground 之前，系统会创建一个 WindowStage。每一个 UIAbility 实例都对应持有一个 WindowStage 实例。

可以在 `onWindowStageCreate` 回调中，设置 UI 页面加载、设置 WindowStage 的事件订阅。

```js
import UIAbility from '@ohos.app.ability.UIAbility';
import window from '@ohos.window';

export default class EntryAbility extends UIAbility {
    ...

    onWindowStageCreate(windowStage: window.WindowStage) {
        // 设置UI页面加载
        // 设置WindowStage的事件订阅（获焦/失焦、可见/不可见）
        ...

        windowStage.loadContent('pages/Index', (err, data) => {
            ...
        });
    }
    ...
}
```

例如用户打开游戏应用，正在打游戏的时候，有一个消息通知，打开消息，消息会以弹窗的形式弹出在游戏应用的上方，此时，游戏应用就从获焦切换到了失焦状态，消息应用切换到了获焦状态。对于消息应用，在 onWindowStageCreate 回调中，会触发获焦的事件回调，可以进行设置消息应用的背景颜色、高亮等操作。

### Foreground

_UIAbility 切换至前台触发_

`onForeground` 回调，在 UIAbility 的 UI 页面可见之前，即 UIAbility 切换至前台时触发。可以在 onForeground 回调中申请系统需要的资源，或者重新申请在 onBackground 中释放的资源

### Background

_UIAbility 切换至后台触发_

`onBackground` 回调，在 UIAbility 的 UI 页面完全不可见之后，即 UIAbility 切换至后台时候触发。可以在 onBackground 回调中释放 UI 页面不可见时无用的资源，或者在此回调中执行较为耗时的操作，例如状态保存等。

```js
import UIAbility from '@ohos.app.ability.UIAbility';
import window from '@ohos.window';

export default class EntryAbility extends UIAbility {
    ...

    onForeground() {
        // 申请系统需要的资源，或者重新申请在onBackground中释放的资源
        ...
    }

    onBackground() {
        // 释放UI页面不可见时无用的资源，或者在此回调中执行较为耗时的操作
        // 例如状态保存等
        ...
    }
}
```

例如用户打开地图应用查看当前地理位置的时候，假设地图应用已获得用户的定位权限授权。在 UI 页面显示之前，可以在 onForeground 回调中打开定位功能，从而获取到当前的位置信息。

当地图应用切换到后台状态，可以在 onBackground 回调中停止定位功能，以节省系统的资源消耗。

### WindowStageDestroy

_在 UIAbility 实例销毁之前，则会先进入 `onWindowStageDestroy` 回调_

我们可以在该回调中释放 UI 页面资源。

### Destroy

_在 UIAbility 销毁时触发_

可以在 `onDestroy` 回调中进行系统资源的释放、数据的保存等操作。

## router

### 页面跳转和参数接收

```js
import router from "@ohos.router";

router.pushUrl({
  url: "pages/Second",
  params: {
    src: "Index页面传来的数据",
  },
});
```

```js
import router from '@ohos.router';

@Entry
@Component
struct Second {
  @State src: string = (router.getParams() as Record<string, string>)['src'];
  // 页面刷新展示
  ...
}
```

### mode 参数

_可以将 mode 参数配置为`router.RouterMode.Single`单实例模式和`router.RouterMode.Standard`多实例模式_

在单实例模式下：如果目标页面的 url 在页面栈中已经存在同 url 页面，离栈顶最近同 url 页面会被移动到栈顶，移动后的页面为新建页，原来的页面仍然存在栈中，页面栈的元素数量不变；如果目标页面的 url 在页面栈中不存在同 url 页面，按多实例模式跳转。

?> 当页面栈的元素数量较大或者超过 `32` 时，可以通过调用 `router.clear()`方法清除页面栈中的所有历史页面，仅保留当前页面作为栈顶页面。

```js
router.replaceUrl(
  {
    url: "pages/Second",
    params: {
      src: "Index页面传来的数据",
    },
  },
  router.RouterMode.Single
);
```

### 页面返回和参数接收

可以通过调用 `router.back()`方法实现返回到上一个页面，或者在调用 `router.back()`方法时增加可选的 options 参数（增加 url 参数）返回到指定页面。

?> 1、调用 `router.back()`返回的目标页面需要在页面栈中存在才能正常跳转。  
2、例如调用 `router.pushUrl()`方法跳转到 Second 页面，在 Second 页面可以通过调用 router.back()方法返回到上一个页面。  
3、例如调用 `router.clear()`方法清空了页面栈中所有历史页面，仅保留当前页面，此时则无法通过调用 router.back()方法返回到上一个页面。

```js
router.back();

router.back({ url: "pages/Index" });

router.back({
  url: "pages/Index",
  params: {
    src: "Second页面传来的数据",
  },
});
```

?> 调用 router.back()方法，不会新建页面，返回的是原来的页面，在原来页面中@State 声明的变量不会重复声明，以及也不会触发页面的 aboutToAppear()生命周期回调，因此无法直接在变量声明以及页面的 aboutToAppear()生命周期回调中接收和解析 router.back()传递过来的自定义参数。

#### router.enableBackPageAlert() 不懂

```js
router.enableBackPageAlert({
  message: "Message Info",
});
```

## 启动模式

### singleton（单实例模式）

_默认情况下的启动模式_

当用户打开浏览器或者新闻等应用，并浏览访问相关内容后，回到桌面，再次打开该应用，显示的仍然是用户当前访问的界面。

这种情况下可以将 UIAbility 配置为 singleton（单实例模式）。**每次调用 startAbility()方法时，如果应用进程中该类型的 UIAbility 实例已经存在，则复用系统中的 UIAbility 实例，系统中只存在唯一一个该 UIAbility 实例。**

singleton 启动模式的开发使用，在 module.json5 文件中的“launchType”字段配置为“singleton”即可:

```json
{
   "module": {
     ...
     "abilities": [
       {
         "launchType": "singleton",
         ...
       }
     ]
  }
}
```

### multiton（多实例模式）

用户在使用分屏功能时，希望使用两个不同应用（例如备忘录应用和图库应用）之间进行分屏，也希望能使用同一个应用（例如备忘录应用自身）进行分屏。

这种情况下可以将 UIAbility 配置为 multiton（多实例模式）。**每次调用 startAbility()方法时，都会在应用进程中创建一个该类型的 UIAbility 实例。**

multiton 启动模式的开发使用，在 module.json5 文件中的“launchType”字段配置为“multiton”即可:

```json
{
   "module": {
     ...
     "abilities": [
       {
         "launchType": "multiton",
         ...
       }
     ]
  }
}
```

### specified（指定实例模式）

用户打开文档应用，从文档应用中打开一个文档内容，回到文档应用，继续打开同一个文档，希望打开的还是同一个文档内容；以及在文档应用中新建一个新的文档，每次新建文档，希望打开的都是一个新的空白文档内容。

这种情况下可以将 UIAbility 配置为 specified（指定实例模式）。**在 UIAbility 实例新创建之前，允许开发者为该实例创建一个字符串 Key，新创建的 UIAbility 实例绑定 Key 之后，后续每次调用 startAbility 方法时，都会询问应用使用哪个 Key 对应的 UIAbility 实例来响应 startAbility 请求。如果匹配有该 UIAbility 实例的 Key，则直接拉起与之绑定的 UIAbility 实例，否则创建一个新的 UIAbility 实例。运行时由 UIAbility 内部业务决定是否创建多实例。**

- 在 module.json5 文件中的“launchType”字段配置为“specified”:

```json
{
   "module": {
     ...
     "abilities": [
       {
         "launchType": "specified",
         ...
       }
     ]
  }
}
```

- 在调用 startAbility()方法的 want 参数中，增加一个自定义参数来区别 UIAbility 实例，例如增加一个“instanceKey”自定义参数。

```js
// 在启动指定实例模式的UIAbility时，给每一个UIAbility实例配置一个独立的Key标识
function getInstance() {
    ...
}
let context:common.UIAbilityContext = ...; // context为调用方UIAbility的UIAbilityContext
let want: Want = {
    deviceId: '', // deviceId为空表示本设备
    bundleName: 'com.example.myapplication',
    abilityName: 'SpecifiedAbility',
    moduleName: 'specified', // moduleName非必选
    parameters: { // 自定义信息
        instanceKey: getInstance(),
    },
}
context.startAbility(want).then(() => {
    ...
}).catch((err: BusinessError) => {
    ...
})
```

- 在被拉起方 UIAbility 对应的 AbilityStage 的 onAcceptWant 生命周期回调中，解析传入的 want 参数，获取“instanceKey”自定义参数。根据业务需要返回一个该 UIAbility 实例的字符串 Key 标识。如果之前启动过此 Key 标识的 UIAbility，则会将之前的 UIAbility 拉回前台并获焦，而不创建新的实例，否则创建新的实例并启动。

```js
onAcceptWant(want: want): string {
    // 在被启动方的AbilityStage中，针对启动模式为specified的UIAbility返回一个UIAbility实例对应的一个Key值
    // 当前示例指的是device Module的EntryAbility
   if (want.abilityName === 'MainAbility') {
        return `DeviceModule_MainAbilityInstance_${want.parameters.instanceKey}`;
    }
    return '';
}
```
