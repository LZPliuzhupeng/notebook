# 内置对 CSS 的支持

## 添加全局样式表

在 pages/\_app.js 文件中导入（import）CSS 文件。

```js
import "../styles.css";

// 新创建的 `pages/_app.js` 文件中必须有此默认的导出（export）函数
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

```js
// components/Dialog.js
import "@reach/dialog/styles.css";

function ExampleDialog() {
  return <Dialog></Dialog>;
}
```

## 添加组件级 CSS

Next.js 通过 `[name].module.css` 文件命名约定来支持 CSS 模块 。

```js
import styles from "./Button.module.css";

export function Button() {
  return (
    <button
      type="button"
      // Note how the "error" class is accessed as a property on the imported
      // `styles` object.
      className={styles.error}
    >
      Destroy
    </button>
  );
}
```

## 对 Sass 的支持

```scss
/* variables.module.scss */
$primary-color: #64FF00

:export {
  primaryColor: $primary-color
}
```

```jsx
// pages/_app.js
import variables from "../styles/variables.module.scss";

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout color={variables.primaryColor}>
      <Component {...pageProps} />
    </Layout>
  );
}
```

## CSS-in-JS

### 内联样式

可以使用任何现有的 CSS-in-JS 解决方案。 最简单的一种是内联样式：

```jsx
function HiThere() {
  return <p style={{ color: "red" }}>hi there</p>;
}

export default HiThere;
```

### styled-jsx

```jsx
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  );
}

export default HelloWorld;
```

!> `styled-jsx`以支持作用域隔离（isolated scoped）的 CSS。 此目的是支持类似于 Web 组件的 “影子（shadow）CSS”，但不幸的是 **不支持服务器端渲染且仅支持 JS**。
