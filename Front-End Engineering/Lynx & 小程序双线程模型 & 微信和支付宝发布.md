- ReactLynx 为 Lynx 官方提供的 React 实现之一，其基于 Preact，提供与标准 React17 基本一致的 API 和行为（直接复用绝大部分 React 生态），只需将 `react`  包替换为  `@lynx-js/react`，通过将 React 代码转换为 Lynx 引擎命令式 API 调用实现原生 UI 渲染且存在操作系统适配差异，通过 Lynx 原生元件替代 HTML 标签，Lynx 全局对象对应 API 实现 Web 端对应功能，未实现功能通过 `NativeModules`  和自定义元件扩展，Lynx 不依赖虚拟 DOM，而将 UI 描述为 AST 并在在 JSON 结构 AST 上执行 Diff，由 C++ 实现的渲染引擎解析并计算差异，通过高性能序列化协议将结果返回给 Native 层，在编译时折叠 JSX 常量以减少运行时开销
- ReactLynx 开创性引入双线程 React 优化，主线程由 PrimJS 引擎驱动，执行 UI 渲染、高优先级任务和主线程脚本，后台线程由 React 运行时驱动，执行业务逻辑、完整 React 渲染和部分 Reconciliation 逻辑即 Off-main-thread Reconciliation。在渲染阶段，主线程优先完成第一屏展现，后台线程并行渲染并构建节点树，后续通过比较两线程树结构以确保一致性，组件生命周期仅在后台线程异步执行，与传统 React 调用时机存在差异且不支持  `useLayoutEffect`，需通过  `main-thread:bindlayoutchange`  获取页面布局结果
- ReactLynx 存在严格的线程代码执行规则：事件处理函数、`effect` 副作用函数、`ref`  属性、`useImperativeHandle` 和标记为 background only 的函数仅在后台线程执行；而标记为 main thread 的函数即主线程脚本则用于跟手动画和手势处理，通过 `main-thread:`  命名空间绑定事件，直接操作  `MainThread.Element`  对象并支持跨线程调用

业界通用迁移方案：

基于 ReactLynx 底层特性，结合工具链自动化与架构层封装，构建"无感迁移 + 多端兼容 + 性能优化"的通用方案，核心从自动化迁移工具链、生态适配中间层、双线程治理体系、性能优化四个维度展开：

一、自动化迁移工具链

针对 ReactLynx 的核心差异，基于 Babel AST 解析 + ESLint 规则校验构建自动化转换工具，彻底替代手动修改：

1. 依赖 & API & 元件替换

- 依赖：`import { xx } from 'react'` -> `import { xx } from '@lynx-js/react'`，处理 React 18 → 17 的 API 降级如 `useId` 自动替换为 `uuid` 库
- API：检测 `window/document` 相关调用如 `window.location.reload` 并替换为对应的 Lynx API `lynx.reload`，未实现功能通过 `NativeModules`  和自定义元件扩展

* 元件：`div` → `view`、`span` → `text`、`img` → `image`，样式兼容 Lynx

二、生态适配中间层

针对 React 生态与 Lynx 的兼容性问题，构建中间层封装 + 适配器模式，实现上层代码"无感复用"：

1. 路由生态：封装 `@lynx-adapter/react-router`，适配 Lynx"单 bundle 无法在多页面间跳转"的限制，底层通过 "原生导航 + bundle 间通信"实现跨 bundle 路由，上层保持 React Router v6 的 `BrowserRouter/useNavigate` API；通过配置文件定义路由与 Lynx bundle 的映射关系，中间层自动处理路由跳转 → 原生唤起目标 bundle→ 参数透传，上层代码无需修改路由逻辑
2. 状态管理：封装 `@lynx-adapter/zustand`，将 reducer/action 逻辑自动拆分至后台线程，通过 Lynx Worklet 机制实现后台状态更新 → 主线程 UI 同步，上层保持 `useSelector/dispatch`；基于 Lynx 全局事件系统封装`createCrossThreadStore` 以支持状态在主线程与后台线程间同步通信，自动序列化
3. 工具库适配：封装 `@lynx-adapter/axios`，底层替换 Axios XMLHttpRequest 为 Lynx 原生网络 API，支持 Lynx 离线缓存和超时重试等特性，上层保持 `axios.get` 调用方式；对依赖 `document` 的库，封装 `NativeModules` 适配器以调用原生能力实现类似功能

三、双线程治理

针对 ReactLynx 严格的双线程执行规则，构建"编译阶段校验 + 运行阶段监控 + 开发阶段提示"的全链路治理体系，避免线程违规：

1. 编译阶段：基于 TypeScript 类型注解与 Babel 插件，自动检测和标记后台专属代码并拆分至后台线程 Bundle，通过静态分析检测跟手动画、手势相关等代码，自动标记 `'main thread'` 并绑定 `main-thread:` 命名空间
2. 运行阶段：封装 Lynx 全局错误监听，捕获主线程调用后台 API 错误，自动降级于后台线程执行，上报监控平台标记违规代码位置
3. 开发阶段：开发 VSCode 插件，基于 ReactLynx 规则在代码编写时实时提示

四、性能优化

小程序：

小程序的 WebView 视图层与 App Service 逻辑层分离，通过 Native 层桥接跨线程通信（基于 JSON 序列化 / 反序列化），视图层负责 UI 渲染和用户交互，逻辑层负责业务逻辑，无法直接操作 DOM，视图层通过 JavaScript 向逻辑层发送请求并等待逻辑层返回结果后渲染页面

跨端框架扮演翻译角色：

1. 编译时转换：将跨端框架源码转换为 AST -> 根据平台差异应用对应转换规则，替换元件、依赖和 API -> 生成目标代码
2. 运行时适配：模拟浏览器环境模拟，适配器模式（业务侧调用跨端框架 API，根据运行时环境抹平平台差异） + 条件编译（编写平台特有代码）
