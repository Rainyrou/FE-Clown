React16 是一个重大版本更新，标志着 React 的一个新时代

1. Fiber 架构
2. 新的生命周期方法：为与异步渲染兼容，React 16 引入新的生命周期方法如 `getDerivedStateFromProps` 和 `getSnapshotBeforeUpdate`
3. Fragment 和字符串返回类型：React16 允许组件从其 `render` 方法返回多个元素 `Fragment` 及字符串或数字类型，避免添加额外的 DOM 节点
4. Portals：提供一种将子节点渲染到存在于父组件以外的 DOM 节点的方式
5. 错误处理机制：React15 引入一个新的错误边界的概念，通过在组件中定义 `unstable_handleError` （React16 更名为 `componentDidCatch`）来实现组件捕获子组件树中发生的 JavaScript 错误，并记录它们，展示一个回退的 UI 而非让整个组件树崩溃
6. SSR：React16 对服务端渲染进行优化，支持流式渲染，允许 HTML 在服务端生成的同时发送到浏览器
