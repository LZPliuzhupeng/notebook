# data-fetching

- `getStaticProps` (Static Generation): Fetch data at **build time**.
- `getStaticPaths` (Static Generation): Specify dynamic routes to pre-render pages based on data.
- `getServerSideProps` (Server-side Rendering): Fetch data on **each request**.

## `getStaticProps`

```js
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  };
}
```

### context

- The `context` parameter is an object containing the following keys：

  - `params`包含动态路由的参数，如果页面为`[id].js`, params 为 `{id:...}`
  - `preview`
  - `previewData`
  - `locale`
  - `locales`
  - `defaultLocale`

### return an object

- getStaticProps should `return` an object with:

  - `props` - 一个可选对象，包含将被页面组件接收的参数。它应该是一个可序列化的对象。
  - `revalidate` - 当一个请求进来时尝试重新生成页面, 每 10 秒最多一次 revalidate: 10.

    当向在构建时预呈现的页面发出请求时，它将首先显示缓存的页面。  
    在初始请求之后和 10 秒之前对页面的任何请求也会被缓存并且是即时的。  
    在 10 秒窗口之后，下一个请求仍将显示缓存的(过期的)页面 js 在后台触发页面的再生。  
    成功生成页面后，Next.js 将使缓存失效并显示更新后的产品页面。  
    如果后台重新生成失败，旧页面将保持不变。  
    当向尚未生成的路径发出请求时，Next.js 将在第一个请求时服务器端呈现页面。以后的请求将从缓存中提供静态文件。

  - `notFound` - 一个可选的布尔值，允许页面返回 404 状态和页面。

    ```js
    export async function getStaticProps(context) {
      const res = await fetch(`https://.../data`);
      const data = await res.json();

      if (!data) {
        return {
          notFound: true,
        };
      }

      return {
        props: { data }, // will be passed to the page component as props
      };
    }
    ```

  - `redirect` - 一般是在服务器端渲染（SSR）使用

    ```js
    export async function getStaticProps(context) {
      const res = await fetch(`https://...`);
      const data = await res.json();

      if (!data) {
        return {
          redirect: {
            destination: "/new-page", // 重定向的目标页面路径
            permanent: false, // 可选， 是否为永久重定向，默认为 false。 永久重定向会在浏览器中缓存，下次访问时会直接跳转到目标页面，而临时重定向会再次发送请求到目标页面。
            statusCode: 307, // 可选， 重定向时返回的状态码，默认为 307
          },
        };
      }

      return {
        props: { data }, // will be passed to the page component as props
      };
    }
    ```

    !> 注意:目前不允许在构建时重定向，如果在构建时知道重定向，则应在 `next.config.js` 配置

    ```js
    // next.config.js
    module.exports = {
      async redirects() {
        return [
          {
            source: "/about",
            destination: "/",
            permanent: true,
          },
        ];
      },
    };
    ```

### 例子

```js
// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries. See the "Technical details" section.
export async function getStaticProps() {
  // Call an external API endpoint to get posts.
  // You can use any data fetching library
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // By returning { props: { posts } }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

### 细节

- 仅在构建时运行
- 静态地生成 HTML 和 JSON

  当在构建时预渲染带有 `getStaticProps` 的页面时，除了页面 HTML 文件外，Nextjs 还生成一个 JSON 文件，其中包含运行 getStaticProps 的结果。

  这个 JSON 文件将通过 `next/link` 或 `next/router` 在客户端路由中使用。当您导航到使用 getStaticProps 预渲染的页面时，Next.js 将获取该 JSON 文件(在构建时预计算)并将其用作页面组件的参数。

  这意味着客户端页面转换将不会调用 getStaticProps，因为只使用导出的 JSON。

  当使用增量静态生成时，getstaticpropss 将在带外执行，以生成客户端导航所需的 JSON。您可能会看到对同一页面发出多个请求，但是，这是有意为之的，不会影响最终用户的性能。

- 只能在页面导出，并且使用`export async function getStaticProps() {}`
- 在测试环境 (`next dev`), 每次请求都会调用
- 预览模式

  在某些情况下，您可能希望暂时绕过静态生成，并在请求时而不是构建时呈现页面。

  例如，您可能正在使用无头 CMS，并希望在发布草稿之前预览草稿。

## `getStaticPaths`

如果页面具有动态路由并使用“getStaticProps”，则需要定义一个必须在构建时呈现为 HTML 的路径列表。

```js
export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // See the "paths" section below
    ],
    fallback: true, false, or 'blocking' // See the "fallback" section below
  };
}
```

### `paths` 必需的

paths 键决定了哪些路径将被预渲染。例如，假设你有一个页面使用名为 pages/posts/[id].js'的动态路由。如果您从该页导出 getstaticpaths 并返回以下路径:

```js
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
```

然后 Next.js 将在构建时使用 `pages/posts/[id].js` 中的 page 组件静态地生成` posts/1`和 `posts/2`。

### `fallback` 必需的

如果 fallback 为 false，则 getStaticPaths 未返回的任何路径都将导致 404 页面。

如果你有少量的路径要预渲染，你可以这样做——所以它们都是在构建时静态生成的。

当不经常添加新页面时，它也很有用。如果向数据源中添加了更多项，并且需要呈现新页面，则需要再次运行构建。

```js
// pages/posts/[id].js

function Post({ post }) {
  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }));

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false };
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // Pass post data to the page via props
  return { props: { post } };
}

export default Post;
```

- **fallback: false**：

  当设置为 false 时，表示只会生成 paths 数组中指定的静态路径页面。
  如果请求的路径不在 paths 数组中，Next.js 会返回 404 页面。

- **fallback: true**：

  当设置为 true 时，表示除了 paths 数组中指定的静态路径页面外，还会生成未指定的路径的静态页面。
  当访问未指定的路径时，Next.js 会先尝试生成该路径对应的静态页面。如果找不到静态页面，它会在客户端渲染（CSR）的情况下动态生成页面并返回。
  这种情况下，页面的生成可能会有延迟，并且每个未指定的路径只会生成一次。生成的页面会被缓存，并在下一次请求时返回静态页面。

- **fallback: "blocking"** ：

  当设置为 'blocking' 时，表示在生成未指定的路径的静态页面时会阻塞请求，直到页面生成完毕。
  当访问未指定的路径时，Next.js 会在服务器端生成该路径对应的静态页面，并在生成完毕后返回页面，而不是使用客户端渲染。
  这种情况下，页面的生成可能会有一些延迟，但是返回的是完整的静态页面，无需再进行客户端渲染。

!> `next export`使用时不支持 Fallback: true '

## `getServerSideProps`

```js
export async function getServerSideProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  };
}
```

### context

- The `context` parameter is an object containing the following keys:

  - params
  - req
  - res
  - query
  - preview
  - previewData
  - resolvedUrl
  - locale
  - locales
  - defaultLocale

### return an object

- getServerSideProps should `return` an object with:
  - props
  - notFound
  - redirect

### 例子

```js
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // Pass data to the page via props
  return { props: { data } };
}

export default Page;
```
