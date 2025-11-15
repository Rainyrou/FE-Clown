CSR：客户端请求指定页面接口并获取所有数据，服务端返回空的 HTML 和 JavaScript 文件，客户端加载并执行 JavaScript 生成 DOM 并动态渲染页面（浏览器渲染）

SSR：服务端请求指定页面接口并获取所有数据，渲染对应静态 HTML 和 CSS 并返回给客户端，客户端直接渲染页面并通过水合即 JavaScript 加载并执行后激活这些静态 HTML 使其具有交互性

* 静态渲染：路由在构建时或后台 ISR 时渲染，采用完全缓存策略即缓存所有数据，缓存结果于 CDN 或 Data Cache  中
* 动态渲染：路由在各个请求到达时按需渲染，执行完整的服务端渲染流程，采用混合缓存策略即缓存部分数据
* 流式渲染：服务端将页面 HTML 拆分为小块并逐步返回给客户端，无需等待全量数据加载后才渲染页面

SSG：服务端提前构建编译并生成静态 HTML，用户访问时直接返回该文件

ISR：增量静态再生，Next.js 结合 SSG 优势按需编译更新并返回 HTML

技术选型：静态内容选 SSG，半静态选 ISR，实时动态选 SSR，交互密集选 CSR

* CSR -> 后台系统 + 在线编辑器
* SSR -> 商品详情页 + 个人中心
* SSG -> 公司官网 + 技术博客 + 产品文档 + 营销落地页
* ISR -> SSG + SSR 

检测水合失败：

* Hydration 容错机制（React18）：从根节点开始递归遍历服务器端渲染的 HTML 与客户端 React 生成的虚拟 DOM，比较两者的标签类型、文本、属性和层级关系等，当检测到不匹配时通过浏览器控制台输出对应错误信息
* JS 全局错误捕获机制
* DOM 渲染检测

SSR 水合失败兜底：

* React Error Boundary（React16）：捕获子组件树的 JavaScript 错误并以回退 UI 为兜底（若在 Suspense 边界中则以 Suspense fallback 兜底），当水合失败时，React 在错误边界中通过 `componentDidCatch` 和 `resetErrorBoundary` 方法自定义错误处理逻辑并重置错误边界状态，重新执行水合过程
* Hydration 容错机制（React18）：渐进式水合 + 选择性水合 + 自动检测和修复错误机制，当水合失败时，React 通过 `popHydrationState` 方法抛出错误并通过内部错误处理机制捕获，在 `ReactFiberWorkLoop.new.js` 文件自定义错误处理逻辑，通过服务器端文本替换为客户端文本以修复文本差异，通过更新相应属性值以修复属性差异，通过销毁旧组件树并构造新组件树以修复 ​​DOM 结构差异，若自动修复失败，则以 CSR 替代 SSR

Next.js SSR：

* 服务端根据路由片段和 Suspense Boundary 将页面拆分为多个独立渲染单元，React 将服务端组件的渲染结果转换为RSC Payload 即服务端组件渲染树的二进制表示（服务端组件自身的 HTML 片段 + 客户端组件对应位置占位符及其 JavaScript 文件引用 + 由服务端组件返回给客户端组件的 Props）
* Next.js 通过 RSC Payload 和客户端组件的 JavaScript 在服务端预渲染 HTML 并返回给客户端，RSC Payload 用于协调服务端组件和客户端组件的位置关系、同步 Props 数据并更新 DOM，客户端组件的 JavaScript 用于水合
* 服务端组件间通过 `fetch` 和 React `cache` 函数共享数据，
* 由于服务端组件与客户端组件的渲染机制存在差异，第三方库中若包含客户端特性代码如 `useState`、`useEffect` 和 `createContext` 等则以 `"use client"` 标记为客户端组件，再在服务端组件中引用