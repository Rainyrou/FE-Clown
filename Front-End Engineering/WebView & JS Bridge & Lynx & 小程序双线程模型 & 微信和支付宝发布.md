WebView 为 Mobile 端操作系统提供的嵌入式浏览器渲染引擎组件，其封装移动设备内置的高性能浏览器内核如 iOS WebKit，Android Chromium，其不提供地址栏、导航栏和书签管理器，在功能上为由 Native 代码创建和控制、用于展示 Web 内容的容器视口，类比页面的  `<iframe>`  标签，但权限和集成度远高于后者，WebView 运行于受控沙箱，Native 代码运行于操作系统，WebView 和 Native 共用原生方法，通过 JSBridge 异步通信

- JavaScript 通过触发 URL Scheme 或执行由 Native 注入的全局对象上的方法向 Native 发送请求，Native 端的消息分发器通过 Wrapper/Decorator 拦截请求，解析 URL Scheme 或全局对象的参数和方法并执行相应 Native 代码
- Native 端通过调用 WebView API 如 `evaluateJavascript` 向 JavaScript 发送请求，通过 Native 端 iOS Method Swizzling 或 Android 动态代理/Hook 技术封装  `evaluateJavaScript`  方法或 JavaScript 端重写全局函数/监听全局事件拦截请求，统一消息格式并执行对应 JavaScript 代码

判断 WebView 加载完成：

* iOS 和 Android WebView 监控页面加载过程的回调函数如 `onPageFinished/onPageStarted`
* iOS 和 Android WebView 监控页面加载实时进度的回调函数如 `onProgressChanged`

JS Bridge 跨多 APP 共用：通信机制的设计和实现（IPC/URL Scheme + 统一协议和接口标准 + WebView 和共享组件 SDK + 共享 Native 功能和服务）

Lynx：

- ReactLynx 为 Lynx 官方提供的 React 实现，基于 Preact，提供与 React17 基本一致的 API 和行为，通过将 React 代码转换为 Lynx 引擎命令式 API 调用实现原生 UI 渲染，通过 Lynx 原生元件替代 HTML 标签，Lynx 全局对象对应 API 实现 Web 端对应功能，未实现功能通过原生模块和自定义元件自行扩展，Lynx 不依赖虚拟 DOM，将 UI 描述为 AST 并在其上执行 Diff，解析并计算 DOM 节点差异，通过高性能序列化协议将结果返回给 Native 端，在编译时折叠 JSX 常量以减少运行时开销
- ReactLynx 引入双线程 React 优化，主线程由 PrimJS 引擎驱动，执行 UI 渲染、高优先级任务和主线程脚本，后台线程由 React 运行时驱动，执行业务逻辑、完整 React 渲染和 Off-main-thread Reconciliation。在渲染阶段，主线程完成首屏，后台线程并行渲染并构建节点树，通过比较两线程树结构以确保一致性，在后台线程异步执行组件生命周期，与传统 React 调用时机存在差异且不支持  `useLayoutEffect`，通过  `main-thread:bindlayoutchange`  获取页面布局效果
- ReactLynx 存在严格的线程代码执行规则：通过条件判断语句将代码写入对应线程，跟手动画、手势处理逻辑和标记为 main thread 的函数在主线程执行；事件处理函数、`effect` 副作用函数、`ref`  属性、`useImperativeHandle` 和标记为 background only 的函数在后台线程执行

迁移方案：

1. 依赖 & 元件 & API 替换

- 依赖：`react` -> `@lynx-js/react`
* 元件：`div` → `view`、`span` → `text`、`img` → `image`，样式兼容 Lynx
- API：BOM/DOM 调用替换为对应 Lynx 调用，未实现功能通过原生模块和自定义元件自行扩展

2. 条件编译：通过条件判断语句将代码写入对应线程，跟手动画、手势处理逻辑和标记为 main thread 的函数在主线程执行；事件处理函数、`effect` 副作用函数、`ref`  属性、`useImperativeHandle` 和标记为 background only 的函数在后台线程执行
3. 构建工具、请求中间件、性能看板、埋点公参
4. 业务链路重构优化，推倒旧页面，创建与之完全不同的页面及样式

小程序：

小程序的 WebView 视图层与 App Service 逻辑层分离，通过 Native 层桥接跨线程通信（基于 JSON 序列化 / 反序列化），视图层负责 UI 渲染和用户交互，逻辑层负责业务逻辑，无法直接操作 DOM，视图层通过 JavaScript 向逻辑层发送请求并等待逻辑层返回结果后渲染页面

跨端框架扮演翻译角色：

1. 编译时转换：将跨端框架源码转换为 AST -> 根据平台差异应用对应转换规则，替换依赖、元件和 API -> 生成目标代码
2. 运行时适配：模拟浏览器环境模拟，适配器模式（业务侧调用跨端框架 API，根据运行时环境抹平平台差异） + 条件编译（编写平台特有代码）
