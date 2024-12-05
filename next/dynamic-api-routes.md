# 动态 API 路由

API 路由 `pages/api/post/[pid].js` 的实现代码如下：

```js
export default function handler(req, res) {
  const { pid } = req.query;
  res.end(`Post: ${pid}`);
}
```

## 索引路由（Index routes）和动态 API 路由

一个通常的 RESTful 模式将设置以下路由：

- `GET api/posts` - 获取 post 列表，可能还有分页
- `GET api/posts/12345` - 获取 id 为 12345 的 post

- 方案 1：
  - `/api/posts.js`
  - `/api/posts/[postId].js`
- 方案 2：
  - `/api/posts/index.js`
  - `/api/posts/[postId].js`

## 捕获所有 API 的路由

通过在方括号内添加三个英文句点 (`...`) 即可将 API 路由扩展为能够捕获所有路径的路由

`pages/api/post/[...slug].js` 匹配 `/api/post/a`，也匹配 `/api/post/a/b`、`/api/post/a/b/c` 等

!> 注意：`slug` 只是形参并不是必须使用的，也可以使用 `[...param]`

query 对象：

```js
{ "slug": ["a"] } // - /api/post/a

{ "slug": ["a", "b"] } // - /api/post/a/b
```

## 可选的捕获所有 API 的路由

通过将参数放在双方括号中 (`[[...slug]]`) 可以使所有路径成为可选路径。

例如， `pages/api/post/[[...slug]].js` 将匹配 `/api/post`、`/api/post/a`、`/api/post/a/b` 等。

catch all 和可选 catch all 路由的主要区别在于，对于可选路由，不带参数的路由也会被匹配(在上面的例子中是/api/post)。

该 query 对象如下所示：

```js
{ } // GET `/api/post` (empty object)
{ "slug": ["a"] } // `GET /api/post/a` (single-element array)
{ "slug": ["a", "b"] } // `GET /api/post/a/b` (multi-element array)
```

## 注意事项

- 预定义的 API 路由优先于动态 API 路由，而动态 API 路由优先于捕获所有 API 的路由。看下面的例子：
  - `pages/api/post/create.js` - 将匹配 `/api/post/create`
  - `pages/api/post/[pid].js` - 将匹配 `/api/post/1`, `/api/post/abc` 等，但不匹配 `/api/post/create`
  - `pages/api/post/[...slug].js` - 将匹配 `/api/post/1/2`,` /api/post/a/b/c` 等，但不匹配 `/api/post/create`、`/api/post/abc`
