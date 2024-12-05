# 页面（Pages）

在 Next.js 中，一个 page（页面） 就是一个从 `.js`、`jsx`、`.ts` 或 `.tsx` 文件导出（export）的 React 组件 ，这些文件存放在 `pages` 目录下。每个 page（页面）都使用其文件名作为路由（route）。

示例： 如果你创建了一个命名为 `pages/about.js` 的文件并导出（export）一个如下所示的 React 组件，则可以通过 `/about` 路径进行访问。

```js
function About() {
  return <div>About</div>;
}

export default About;
```

## 具有动态路由的页面

Next.js 支持具有动态路由的 pages（页面）。例如，如果你创建了一个命名为 pages/posts/[id].js 的文件，那么就可以通过 posts/1、posts/2 等类似的路径进行访问。

```js
import { useRouter } from "next/router";

const Post = () => {
  const router = useRouter();
  const { id } = router.query;

  return <p>Post: {id}</p>;
};

export default Post;
```

## 两种形式的预渲染

Next.js 具有两种形式的预渲染： **静态生成（Static Generation）** 和 **服务器端渲染（Server-side Rendering）**。这两种方式的不同之处在于为 page（页面）生成 HTML 页面的 时机 。

- **静态生成（推荐）**： HTML 是在 **构建时（build time）** 生成的，并重用于每个页面请求。要使页面使用“静态生成”，只需导出（export）页面组件或导出（export） `getStaticProps` 函数（如果需要还可以导出 `getStaticPaths` 函数）。对于可以在用户请求之前预先渲染的页面来说，这非常有用。你也可以将其与客户端渲染一起使用以便引入其他数据。
- **服务器端渲染**： HTML 是在 **每个页面请求** 时生成的。要设置某个页面使用服务器端渲染，请导出（export） `getServerSideProps` 函数。由于服务器端渲染会导致性能比“静态生成”慢，因此仅在绝对必要时才使用此功能。

重要的是，Next.js 允许你为每个页面 **选择** 预渲染的方式。你可以创建一个 “混合渲染” 的 Next.js 应用程序：对大多数页面使用“静态生成”，同时对其它页面使用“服务器端渲染”。

## 静态生成

如果一个页面使用了 **静态生成**，在 **构建时（build time）** 将生成此页面对应的 HTML 文件 。这意味着在生产环境中，运行 `next build` 时将生成该页面对应的 HTML 文件。然后，此 HTML 文件将在每个页面请求时被重用，还可以被 CDN 缓存。

在 Next.js 中，你可以静态生成 **带有或不带有数据** 的页面。

#### 不带数据

```js
function About() {
  return <div>About</div>;
}

export default About;
```

#### 带数据

- 页面 **内容** 取决于外部数据：使用 `getStaticProps`。
- 页面 **paths（路径）** 取决于外部数据：使用 `getStaticPaths` （通常还要同时使用 `getStaticProps`）。

详情看[getStaticProps](/next/data-fetching?id=getstaticprops)、[getStaticPaths](/next/data-fetching?id=getStaticPaths)

## 服务器端渲染

_也被称为 “SSR” 或 “动态渲染”_

如果 page（页面）使用的是 **服务器端渲染**，则会在 **每次页面请求时** 重新生成页面的 HTML 。

要对 page（页面）使用服务器端渲染，你需要 `export` 一个名为 `getServerSideProps` 的 `async` 函数。服务器将在每次页面请求时调用此函数。

详情看[getServerSideProps](/next/data-fetching?id=getServerSideProps)
