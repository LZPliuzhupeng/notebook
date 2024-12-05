# Script Optimization

`next/script`是 HTML Script 元素的扩展。它使开发人员可以在应用程序的任何地方设置第三方脚本的加载优先级，而无需直接附加到 next/head。

```js
import Script from "next/script";

export default function Home() {
  return (
    <>
      <Script src="https://www.google-analytics.com/analytics.js" />
    </>
  );
}
```

## Strategy

可以通过使用' strategy '属性来决定何时加载第三方脚本:

- `beforeInteractive` - 在页面交互之前加载
- `afterInteractive`(**default**) - 在页面变成交互式后立即加载
- `lazyOnload` - :空闲时加载

```js
<Script src="https://connect.facebook.net/en_US/sdk.js" strategy="lazyOnload" />
```

## Inline Scripts

脚本组件还支持内联脚本，即不从外部文件加载的脚本。它们可以通过将 JavaScript 放在花括号中来编写:

```js
<Script id="show-banner" strategy="lazyOnload">
  {`document.getElementById('banner').classList.remove('hidden')`}
</Script>
```

或者使用`dangerouslySetInnerHTML`属性

```js
<Script
  id="show-banner"
  dangerouslySetInnerHTML={{
    __html: `document.getElementById('banner').classList.remove('hidden')`,
  }}
/>
```

!> 1、内联脚本只能使用`afterInteractive`和`lazyOnload`策略，内联做到了`beforeInteractive`加载策略将外部脚本的内容注入到初始 HTML 响应中.
2、为了让 Next.js 跟踪和优化脚本，必须定义一个 id 属性

## 加载后执行代码(`onLoad`)

一些第三方脚本要求用户在脚本完成加载后运行 JavaScript 代码，以便实例化内容或调用函数。如果你在加载一个脚本时使用`beforeInteractive`或`afterInteractive`作为加载策略，可以使用 `onLoad` 属性在加载后执行代码:

```js
import { useState } from "react";
import Script from "next/script";

export default function Home() {
  const [stripe, setStripe] = useState(null);

  return (
    <>
      <Script
        id="stripe-js"
        src="https://js.stripe.com/v3/"
        onLoad={() => {
          setStripe({ stripe: window.Stripe("pk_test_12345") });
        }}
      />
    </>
  );
}
```

## 额外的属性

有许多 DOM 属性可以分配给 script 组件不使用的`<script>`元素，比如 nonce 或自定义数据属性。包括任何附加属性都会自动将其转发到最终的、优化的、输出到页面的`<script>`元素。

```js
import Script from "next/script";

export default function Home() {
  return (
    <>
      <Script
        src="https://www.google-analytics.com/analytics.js"
        id="analytics"
        nonce="XUENAJFW"
        data-test="analytics"
      />
    </>
  );
}
```
