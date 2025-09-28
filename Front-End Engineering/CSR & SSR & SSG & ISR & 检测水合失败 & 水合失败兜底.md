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

检测水合失败：

* Hydration 容错机制（React18）：从根节点开始递归遍历服务器端渲染的 HTML 与客户端 React 生成的虚拟 DOM，比较两者的标签类型、文本、属性和层级关系等，当检测到不匹配时通过浏览器控制台输出对应错误信息
* JS 全局错误捕获机制
* DOM 渲染检测

SSR 水合失败兜底：

* React Error Boundary（React16）：捕获子组件树的 JavaScript 错误并以回退 UI 为兜底（若在 Suspense 边界中则以 Suspense fallback 兜底），当水合失败时，React 在错误边界中通过 `componentDidCatch` 和 `resetErrorBoundary` 方法自定义错误处理逻辑并重置错误边界状态，重新执行水合过程
* Hydration 容错机制（React18）：渐进式水合 + 选择性水合 + 自动检测和修复错误机制，当水合失败时，React 通过 `popHydrationState` 方法抛出错误并通过内部错误处理机制捕获，在 `ReactFiberWorkLoop.new.js` 文件自定义错误处理逻辑，通过服务器端文本替换为客户端文本以修复文本差异，通过更新相应属性值以修复属性差异，通过销毁旧组件树并构造新组件树以修复 ​​DOM 结构差异，若自动修复失败，则以 CSR 替代 SSR
