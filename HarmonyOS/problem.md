## 1、terminateSelf 的使用

```js
this.context.terminateSelf();
```

效果：**退出 App**

## `？`2、Stack 容器的使用

```js
Stack() {}
```

## `？`3、@Styles 和 @Extend() 写法区别

```js
@Styles function allSize() {
  .width(Const.THOUSANDTH_1000)
  .height(Const.THOUSANDTH_1000)
}

Tabs()
  .allSize()
```

```js
@Extend(Text) function descStyle () {
  .fontSize($r('app.float.default_14'))
  .fontWeight(Const.FONT_WEIGHT_400)
  .fontFamily($r('app.string.HarmonyHeiTi'))
  .fontColor($r(`app.element.color.titleColor`))
  .width(Const.FULL_WIDTH)
  .lineHeight($r('app.float.default_20'))
  .margin({ top: $r('app.float.default_8') })
}

Text($r('app.string.privacy_title'))
  .descStyle()
```

## `？`4、notificationManager.requestEnableNotification() 的使用

## `？`5、@ohos.hilog 的使用

```js
import hilog from "@ohos.hilog";

hilog.debug(this.domain, this.prefix, this.format, args);
hilog.info(this.domain, this.prefix, this.format, args);
hilog.warn(this.domain, this.prefix, this.format, args);
hilog.error(this.domain, this.prefix, this.format, args);
```

## `？`6、Navigation 的使用

```js
Navigation() {
  Column() {
    TaskDetail()
  }
  .width(Const.THOUSANDTH_1000)
  .height(Const.THOUSANDTH_1000)
}
```

## `？`7、gesture 的使用

```js
Row().gesture(LongPressGesture().onAction(() => {}));
```

## `？`8、子组件怎么接收方法

```js
@Component
export struct TaskCard {
  @Prop taskInfoStr: string = '';
  clickAction: Function = (isClick: boolean) => {};
}
```

```js
TaskCard({
  taskInfoStr: JSON.stringify(item),
  clickAction: (isClick: boolean) => this.taskItemAction(item, isClick),
});
```

## `？`9、@StorageProp 的使用

```js
@Component
export struct BadgePanel {
  @StorageProp('AchievementLevelKey') successiveDays: number = 0;
}
```

## `？`10、@ohos.data.relationalStore 的使用

关系型数据库

```js
import dataRdb from "@ohos.data.relationalStore";
```

## `？`11、windowStage.loadContent 的使用

使用 windowStage.loadContent 为指定 Ability 设置相关的 Page 页面。

```js
// DetailsAbility.ts
...
export default class DetailsAbility extends UIAbility {
  ...
  onWindowStageCreate(windowStage) {
    ...
    windowStage.loadContent('pages/DetailsPage', (err, data) => {
      if (err.code) {
        hilog.error(DETAIL_ABILITY_DOMAIN, TAG, 'Failed. Cause: %{public}s', JSON.stringify(err) ?? '');
        return;
      }
      hilog.info(DETAIL_ABILITY_DOMAIN, TAG, 'Succeeded. Data: %{public}s', JSON.stringify(data) ?? '');
    });
  }
  ...
};
```
