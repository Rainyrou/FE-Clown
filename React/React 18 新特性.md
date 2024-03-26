1. 自动批处理
2. 并发渲染：通过 `createRoot` API 实现并发渲染，它允许 React 准备多个版本的 UI，让其在保持界面响应的同时执行如数据获取、代码分割等耗时任务
3. 加强对并发特性的支持：`useDeferredValue`、`useTransition` 等 Hooks
4. startTransition：用于标记非紧急更新，允许 React 延迟处理这些更新
5. useId Hook：用于生成唯一的 ID，它在服务端和客户端渲染中保持一致，避免 hydration 不匹配的问题
6. 新的 SuspenseList 组件：允许开发者控制多个 Suspense 边界的加载顺序
7. 新的 Suspense SSR 架构
