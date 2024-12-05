# bug

## 推特分享

[web-intents](https://developer.twitter.com/en/docs/twitter-for-websites/web-intents/overview)

```html
<!-- 注：下述的twitter:url 链接,即为twitter从这个链接爬取分享的内容  -->
<meta property="twitter:url" content="https://xxxxxx.com" />
<meta name="twitter:title" content="Remove Image Background for Free" />
<meta name="twitter:description" content="Remove Image Background for Free" />
<meta name="twitter:site" content="@PixCut" />
<meta
  property="twitter:image"
  content="https://xxxxxx.com/image_background.jpg"
/>
<meta name="twitter:card" content="summary_large_image" />
```

发推填充内容

```js
const text = `🚀Hello! https://www.wheeloffate.xyz`; // 会爬取url信息， 根据twitter:card 内容替换调对应url
const hashtags = `html,css`; //#html #css
const via = "someone"; //@someone

window.open(
  `https://twitter.com/intent/tweet?text=${encodeURIComponent(
    text
  )}&hashtags=${hashtags}&via=${via}`
);
```

回复

```js
const tweet_id = 463440424141459456;
const related = `twitterdev,twitterapi`; //未知作用
const original_referer_url = ""; //未知作用

window.open(
  `https://twitter.com/intent/post?in_reply_to=${tweet_id}&related=${related}&original_referer=${original_referer_url}`
);
```

## 使用 i18n 库时遇到包含 @ 符号的文本导致报错

```js
//报错信息
Message compilation error: Invalid linked format
Message compilation error: Unexpected lexical analysis in token: 'xxx'
Message compilation error: Unexpected empty linked key
```

**解决：**

1、使用 `@@` 表示单个 @ 符号, 转义符号对 @ 进行转义。

2、使用 Unicode `\u0040` 转义序列来表示 @ 符号

3、使用 HTML 实体编码 `&#64` 来表示 @ 符号

## 谷歌插件`fs.readFileSync is not a function`

<!-- 插入自定义样式报错 -->

```js
export const addLocalhostStyle = () => {
  const fs = require("fs");
  const lhCss = fs.readFileSync("../styles/localhost.css", "utf-8");
  const lhStyle = document.createElement("style");
  lhStyle.textContent = lhCss;
  document.head.appendChild(lhStyle);
};
```

**解决：**
css 文件路径错误 使用`fs.readFileSync("./src/styles/localhost.css", "utf-8");`

## Failed to load response data: No resource with given identifier found

google chrome devTools (F12) Network Fetch/XHR Preview 显示 "Failed to load response data: No resource with given identifier found"

**原因：**
开启了 Preserve log 选项，此选项刷新或跳转时不清除记录，不保留请求结果。

## rem 方案，苹果手机字显示问题（PC 正常）

**原因：**

1、rem 方案 重置了 hmtl 的 font-size

2、PC 显示正常是因为 谷歌内核浏览器限制了最小的 font-size 值

3、苹果手机上，如不设置 fon-size 会继承上一级（fxied 继承 html），字体过小显示不出来

**解决：**  
设置元素 font-size

## 移动端 window.open 有时不生效（或者被当弹窗拦截）

**原因：**
[参考文章](https://blog.csdn.net/susuzhe123/article/details/71108002?spm=1001.2101.3001.6650.9&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-9.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-9.pc_relevant_aa&utm_relevant_index=15)

**解决：**  
手机端用`window.location.href`替代 window.open

## RegExp.test( )结果问题

**RegExp.test( )方法时第一次是 true 第二次却是 false**

**原因：**  
1、lastIndex 属性用于规定下次匹配的起始位置。

2、上次匹配的结果是由方法 RegExp.exec() 和 RegExp.test() 找到的，它们都以 lastIndex 属性所指的位置作为下次检索的起始点。

3、这样，就可以通过反复调用这两个方法来遍历一个字符串中的所有匹配文本。

4、而且需要注意，该属性只有设置标志 g 才能使用。

**解决：**  
1、第一种解决方案：不使用 全局模式

2、第二种解决方案：重置 lastIndex 属性

```js
var s1 = "3206064928：MRLP：3206064928";
var s2 = "MRLP";
var reg = /mrlp/gi;
console.log(reg.test(s1)); //true
console.log(reg.lastIndex); //reg.lastIndex = 15
reg.lastIndex = 0; //这里我将 lastIndex 重置为 0
console.log(reg.test(s2)); //true
```

3、第三种解决方式：改用 match() 来替换 test() 做判断

## axios 特异性跨域问题

出现了非正常跨域

**原因：**  
1、附带身份凭证的请求： `withCredentials: true` 时

- 服务器不能将 Access-Control-Allow-Origin 的值设为通配符“\*”，而应将其设置为特定的域，如：Access-Control-Allow-Origin: https://example.com。
- 服务器不能将 Access-Control-Allow-Headers 的值设为通配符“\*”，而应将其设置为首部名称的列表，如：Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
- 服务器不能将 Access-Control-Allow-Methods 的值设为通配符“\*”，而应将其设置为特定请求方法名称的列表，如：Access-Control-Allow-Methods: POST, GET

**解决：**

- `withCredentials: false`
- 或 `Access-Control-Allow-Origin` 不为 `*`

## js 浮点运算精度问题

```js
0.1 + 0.2 != 0.3;
```

**原因：**  
IEEE-745 浮点数表示法进度问题

**_参考[[ JS 基础 ] JS 浮点数四则运算精度丢失问题 (3)](https://segmentfault.com/a/1190000002613722)_**

**解决：**  
推荐使用`toFixed`。位运算符`>>>`取小整，数据会变小。

## element table 表单校验

**原因：**  
1、`skuTable`直接放在`this`下，`prop`不支持校验  
2、`prop`校验路径错误，不用填写前缀`formData`

**解决：**

```html
<el-form ref="form2" :model="formData" :rules="rules">
  <el-table :data="formData.skuTable">
    <el-table-column>
      <template slot-scope="scope">
        <el-form-item
          :prop="`skuTable.${scope.$index}.couponCode`"
          :rules="rules.couponCode"
        >
        </el-form-item>
      </template>
    </el-table-column>
  </el-table>
</el-form>
```

## 小程序 wxParse 解析（未解决）

小程序 wxParse 解析图片链接有空白符的,解析错误

**原因：**  
`空格`解析成了`,`

**解决：**  
未解决

## 小程序 fixed 区域下使用 scroll-view

手机上层级错乱

**原因：**  
官方原因，不推荐此用法

**解决：**  
未解决

## text-align: justify 不生效

**原因：**  
`text-align`不会处理被打断的行和最后一行

**解决：**

- 添加伪类

  ```css
  ::after {
    content: "";
    width: 100%;
    display: inline-block;
    overflow: hidden;
    height: 0;
    font-size: 0;
  }
  ```

- 使用`text-align-last`（不推荐）

使用 text-align-last，并将其设置为 justify，不过 text-align-last 不是所有浏览器支持。

## bindlongtap 会捕获事件

**原因：**
\"longtap\" event is deprecated. Please use \"longpress\" event instead.

longtap 已弃用，请使用 longpress 代替

**解决：**
小程序 bindlongpress 替换 bindlongtap
