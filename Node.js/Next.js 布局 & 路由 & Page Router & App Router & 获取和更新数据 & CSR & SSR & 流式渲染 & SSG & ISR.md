组件层次结构：

![[Pasted image 20250404232253.png]]

页面为在特定路由上渲染的 UI，布局为在多页面间共享的 UI，切换页面时保存状态而不重新渲染，在 `app` 目录必须包含根布局，而根布局是服务端组件且必须包含 `html` 和 `body` 标签

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

Page Router 基于 `/pages` 目录的约定式路由，各个 `.ts` 文件分别对应一个路由，页面切换时通过 `next/link`  的  `Client-Side Navigation` 实现无刷新跳转，重新加载完整页面模块，通过 `next/router`  管理路由状态，通过 `getStaticProps`、`getServerSideProps` 和 `getStaticPaths` 等函数获取数据且获取数据逻辑与页面组件分离，支持 CSR、SSR 和 SSG
App Router 基于 `/app`  目录，通过文件夹层级定义路由结构，在  `page.js`  或  `route.js`  文件添加至路由段前，路由无法直接访问，在文件顶部添加 `'use client'`  以区分客户端/服务端组件，结合 Suspense 实现流式渲染

![[Pasted image 20250404230908.png]]

获取数据：[Fetching Data | Next.js](https://nextjs.org/docs/app/getting-started/fetching-data)

客户端组件：React `use` 钩子和 SWR 等第三方库
服务端组件：`fetch` 和数据库

更新数据：[Updating Data | Next.js](https://nextjs.org/docs/app/getting-started/updating-data)

图像和字体：[Images and Fonts | Next.js](https://nextjs.org/docs/app/getting-started/images-and-fonts)

CSR：客户端请求指定页面接口并获取所有数据，服务端返回空的HTML和JavaScript文件，客户端加载并执行JavaScript生成DOM并动态渲染页面（浏览器渲染）

SSR：服务端请求指定页面接口并获取所有数据，渲染对应静态HTML和CSS并返回给客户端，客户端直接渲染页面并通过水合即JavaScript 加载并执行后激活这些静态 HTML使其具有交互性，但服务端只能在获取所有数据后才能渲染页面 HTML且客户端React 只有在加载所有页面组件代码后才能进行水合

![[Pasted image 20250405000506.png]]

流式渲染：服务端将页面 HTML 拆分为小块并逐步返回给客户端，无需等待所有数据加载后才能渲染页面

![[Pasted image 20250405002857.png]]

SSG：服务端提前构建编译并生成静态HTML，用户访问时直接返回该文件
ISR：增量静态再生，服务端提前构建编译并生成静态HTML，用户访问时直接返回该文件，结合SSG优势的同时，Next.js 按需编译更新并返回HTML，无需重新构建完整页面

[Rendering | Next.js](https://nextjs.org/docs/app/building-your-application/rendering)

[Caching | Next.js](https://nextjs.org/docs/app/building-your-application/caching)