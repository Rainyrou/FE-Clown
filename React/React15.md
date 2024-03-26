1. 支持 `return null` 从 `render` 方法中返回，允许组件条件性渲染
2. 引入 `PropTypes` 的独立包 `prop-types`，在以后的版本中 `React.PropTypes` 被废弃
3. 错误处理机制：React15 引入一个新的错误边界的概念，通过在组件中定义 `unstable_handleError` （React16 更名为 `componentDidCatch`）来实现组件捕获子组件树中发生的 JavaScript 错误，并记录它们，展示一个回退的 UI 而非让整个组件树崩溃
4. SVG 支持：在 React15 前，在某些场景中使用 SVG 比较麻烦，因为必须将 SVG 标记封装在 `dangerouslySetInnerHTML` 中，而 React15 开始原生支持 SVG
5. SSR：React15 对服务端渲染进行了优化，减少额外标记，通过在服务端渲染输出中省略一些 DOM 属性如 `data-reactid` 实现减小从服务端渲染的页面大小
6. 性能优化
7. 开发体验
