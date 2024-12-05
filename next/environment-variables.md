# 环境变量

## 加载环境变量

Next.js 内置支持将环境变量从 `.env.local` 加载到 `process.env` 中。

```env
DB_HOST=localhost;
DB_USER=myuser;
DB_PASS=mypassword;
```

这回将 `process.env.DB_HOST`、`process.env.DB_USER` 和 `process.env.DB_PASS` 自动加载到 Node.js 环境中，从而允许你在 **Next.js 的数据提取方法** 和 **API 路由** 中使用它们。

例如，在 `getStaticProps` 中：

```js
// pages/index.js
export async function getStaticProps() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  });
  // ...
}
```

!> 注意：为了确保仅服务器可见的密钥类信息的安全，Next.js 将在构建时会将 `process.env.*` 替换为对应的值。 这就意味着 `process.env` 不是标准的 JavaScript 对象，因此你**不能**对其 使用 **对象解构（object destructuring）** 语法。  
环境变量应当以类似 `process.env.PUBLISHABLE_KEY` 方式访问，而 不能 以 `const { PUBLISHABLE_KEY } = process.env` 方式。

!> 注意： Next.js 会在 `.env*` 文件中自动扩展变量（例如 `$VAR`）

```evn
HOSTNAME=localhost
PORT=8080
HOST=http://$HOSTNAME:$PORT
```

!> 如果你尝试使用实际值中带有 `$` 符号的变量，则需要像这样 `\$` 对其进行转义。

```env
# .env
A=abc

# 扩展为 "preabc"
WRONG=pre$A

# 扩展为 "pre$A"
CORRECT=pre\$A
```

## 将环境变量暴露给浏览器

默认情况下，环境变量仅在 Node.js 环境中可用，这意味着它们不会暴露到浏览器端。

为了向浏览器暴露环境变量，你必须在变量前添加 `NEXT_PUBLIC_` 前缀。

```env
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

这将自动将 `process.env.NEXT_PUBLIC_ANALYTICS_ID` 加载到 Node.js 环境中，从而允许你在代码的任何地方使用它。带有前缀 `NEXT_PUBLIC_` 的变量将被内联到发送给浏览器的 JavaScript 代码中。由于这种内联发生在构建时，因此在构建项目时需要设置好各个 `NEXT_PUBLIC_` 前缀的环境变量。

```js
// pages/index.js
import setupAnalyticsService from "../lib/my-analytics-service";

// NEXT_PUBLIC_ANALYTICS_ID can be used here as it's prefixed by NEXT_PUBLIC_
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID);

function HomePage() {
  return <h1>Hello World</h1>;
}

export default HomePage;
```

## 默认环境变量
