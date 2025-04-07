组件层次结构：

![[Pasted image 20250404232253.png]]

页面为在特定路由上渲染的 UI，布局为在多页面间共享的 UI，切换页面时保存状态而不重新渲染，在 `app`  目录必须包含根布局，而根布局是服务端组件且必须包含 `html`  和 `body`  标签

```js
import "./globals.css";

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

![[Pasted image 20250404232533.png]]

路由导航：

- Native History API
- Link 组件
- 客户端组件 `useRouter`
- 服务端组件 `redirect`

动态路由：`[folderName]` [Routing: Dynamic Routes | Next.js](https://nextjs.org/docs/app/building-your-application/routing/dynamic-routes)

平行路由：同时或条件性地渲染同一布局中的一至多个页面，此外可独立流式传输，为各个路由定义独立的错误和加载状态 [Routing: Parallel Routes | Next.js](https://nextjs.org/docs/app/building-your-application/routing/parallel-routes)

![[Pasted image 20250404234303.png]]

![[Pasted image 20250404234512.png]]

![[Pasted image 20250404234629.png]]

拦截路由：在当前路由拦截其他路由并在渲染其页面内容，但通过 URL 直接访问对应路由时，渲染其完整页面而不发生路由拦截 [Routing: Intercepting Routes | Next.js](https://nextjs.org/docs/app/building-your-application/routing/intercepting-routes)

- `(.)` to match segments on the same level
- `(..)` to match segments one level above
- `(..)(..)` to match segments two levels above
- `(...)` to match segments from the root `app` directory

![[Pasted image 20250404235003.png]]

![[Pasted image 20250404235225.png]]

Page Router 基于 `/pages` 目录的约定式路由，各个 `.ts` 文件分别对应一个路由，页面切换时通过 `next/link`  的  `Client-Side Navigation`  实现无刷新跳转，重新加载完整页面模块，通过 `next/router`  管理路由状态，通过 `getStaticProps`、`getServerSideProps` 和 `getStaticPaths` 等函数获取数据且获取数据逻辑与页面组件分离，支持 CSR、SSR 和 SSG
App Router 基于 `/app`  目录，通过文件夹层级定义路由结构，在  `page.js`  或  `route.js`  文件添加至路由段前，路由无法直接访问，在文件顶部添加 `'use client'`  以区分客户端/服务端组件，结合 Suspense 实现流式渲染

![[Pasted image 20250404230908.png]]

获取数据：[Fetching Data | Next.js](https://nextjs.org/docs/app/getting-started/fetching-data)

客户端组件：React `use` 钩子和 SWR 等第三方库
服务端组件：`fetch` 和数据库

更新数据：[Updating Data | Next.js](https://nextjs.org/docs/app/getting-started/updating-data)

图像和字体：[Images and Fonts | Next.js](https://nextjs.org/docs/app/getting-started/images-and-fonts)

客户端组件（SSR + CSR）：在服务端预渲染而后在客户端进行水合，在文件顶部添加 `use client` 即可使用客户端组件，为减小客户端组件 JavaScript 包大小，将客户端组件移至组件树下方即将交互逻辑移至客户端组件而将布局保留为服务端组件

![[Pasted image 20250405103716.png]]

CSR：客户端请求指定页面接口并获取所有数据，服务端返回空的 HTML 和 JavaScript 文件，客户端加载并执行 JavaScript 生成 DOM 并动态渲染页面（浏览器渲染）

服务端组件：Next.js 默认通过服务端组件实现 SSR 而无需额外配置，其在服务端渲染为 HTML 返回给客户端，渲染工作按单路由片段和  Suspense Boundary 拆分为多个小块，而 React 将服务端组件渲染为 React Server Component Payload 即服务端组件渲染树的二进制表示（服务端组件渲染结果 + 客户端组件对应位置占位符及其 JavaScript 文件引用 + 从服务端组件返回给客户端组件的所有 Props），Next.js 通过 RSC Payload 和客户端组件的 JavaScript 在服务端渲染 HTML，RSC Payload 协调服务端组件和客户端组件并更新 DOM，而客户端组件的 JavaScript 用于水合。服务端组件间使用 `fetch` 和 React `cache` 函数共享数据，而无法使用 React Context API 或以通过 props 传递数据，这是因为服务端组件不支持 React 的状态和上下文，此外 Next.js 未将以 `NEXT_PUBLIC` 开头的环境变量替换为空字符串保证服务端专用代码不侵入客户端，我们手动引入 `server-only` 和 `client-only` 包实现类似效果。由于服务端组件与客户端组件的渲染机制存在差异，第三方库中若包含依赖客户端特性的代码如 `useState`、`useEffect` 和 `createContext` 等则以 `"use client"` 包装第三方组件为客户端组件，再在服务端组件中直接使用它们，Next.js 不支持将服务端组件导入到客户端组件中，但支持服务端组件以 props 的形式返回给客户端组件（数据需进行序列化） [Rendering | Next.js](https://nextjs.org/docs/app/building-your-application/rendering)

![[1743821179500.png]]

SSR（静态渲染、动态渲染、流式渲染）：服务端请求指定页面接口并获取所有数据，渲染对应静态 HTML 和 CSS 并返回给客户端，客户端直接渲染页面并通过水合即 JavaScript 加载并执行后激活这些静态 HTML 使其具有交互性，但服务端只能在获取所有数据后才能渲染页面 HTML 且客户端 React 只有在加载所有页面组件代码后才能进行水合

![[Pasted image 20250405000506.png]]

静态渲染：路由在构建时或后台 ISR 时渲染，采用完全缓存策略即缓存所有数据，缓存结果于 CDN 或 Data Cache  中

动态渲染：路由在各个请求到达时按需渲染，执行完整渲染流程，采用混合缓存策略即缓存部分数据

| Dynamic APIs | Data       | Route                |
| ------------ | ---------- | -------------------- |
| No           | Cached     | Statically Rendered  |
| Yes          | Cached     | Dynamically Rendered |
| No           | Not Cached | Dynamically Rendered |
| Yes          | Not Cached | Dynamically Rendered |

Dynamic APIs：

- [`cookies`](https://nextjs.org/docs/app/api-reference/functions/cookies)
- [`headers`](https://nextjs.org/docs/app/api-reference/functions/headers)
- [`connection`](https://nextjs.org/docs/app/api-reference/functions/connection)
- [`draftMode`](https://nextjs.org/docs/app/api-reference/functions/draft-mode)
- [`searchParams` prop](https://nextjs.org/docs/app/api-reference/file-conventions/page#searchparams-optional)
- [`unstable_noStore`](https://nextjs.org/docs/app/api-reference/functions/unstable_noStore)

流式渲染：服务端将页面 HTML 拆分为小块并逐步返回给客户端，无需等待所有数据加载后才能渲染页面

![[Pasted image 20250405002857.png]]

SSG：服务端提前构建编译并生成静态 HTML，用户访问时直接返回该文件
ISR：增量静态再生，服务端提前构建编译并生成静态 HTML，用户访问时直接返回该文件，结合 SSG 优势的同时，Next.js 按需编译更新并返回 HTML，无需重新构建完整页面

| Mechanism           | What                       | Where  | Purpose                                         | Duration                        |
| ------------------- | -------------------------- | ------ | ----------------------------------------------- | ------------------------------- |
| Request Memoization | Return values of functions | Server | Re-use data in a React Component tree           | Per-request lifecycle           |
| Data Cache          | Data                       | Server | Store data across user requests and deployments | Persistent (can be revalidated) |
| Full Route Cache    | HTML and RSC payload       | Server | Reduce rendering cost and improve performance   | Persistent (can be revalidated) |
| Router Cache        | RSC Payload                | Client | Reduce server requests on navigation            | User session or time-based      |

![[Pasted image 20250405174357.png]]

Next.js 扩展  `fetch`  函数以缓存相同请求即在 React 组件树的多个位置为同一数据调用 `fetch` 函数时只执行一次，无需在组件树顶部获取数据并在组件间传递 props，而在所需组件中直接获取数据

![[Pasted image 20250405174614.png]]

在路由中第一次调用特定请求时，其结果未在内存中且为缓存  `MISS`，因此执行该函数以从外部获取数据并将结果缓存于内存中，而后续调用同一请求时为缓存  `HIT`，数据直接从内存获取而不执行该函数，而当路由渲染完成后，内存重置并清除相关请求结果，Request Memoization 适用于在 React 组件树中的 `fetch`  请求的  `GET`  方法，而对于其他场景则通过 React `cache` 函数实现类似效果，由于 Request Memoization 不在服务端请求间共享且仅在渲染期间使用，因此无需重新验证，官方不建议选择退出 [Caching | Next.js](https://nextjs.org/docs/app/building-your-application/caching)

![[Pasted image 20250405174817.png]]

[Optimizing | Next.js](https://nextjs.org/docs/app/building-your-application/optimizing)
