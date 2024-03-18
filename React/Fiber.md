React Fiber 用于增强 React 的性能，尤其是在处理动画和页面渲染等方面，Fiber 实质上是重构 React 虚拟 DOM，每个 React 元素对应一至多个 Fiber 节点，它们构成一个工作单元的树形结构，React 为每个 Fiber 节点标记 Effect，以表示不同类型的工作，每个 Fiber 节点代表一个工作单元，React 利用浏览器的空闲时间，通过 requestIdleCallback API 实现任务的分割和优先级排序，并依照优先级顺序执行这些工作单元，它使 React 可在渲染树的更新过程中中断和复用渲染任务，增强 React 渲染的可预测性。React 在渲染时构建两棵 Fiber 树，一棵为在屏幕上显示的 current tree，另一棵为正在内存中构建的工作树 work-in-progress tree。这种方式使 React 可在内存中构建新树，并在完成后进行快速切换。此外，渲染过程分为两个阶段，在协调阶段，React 异步计算新树变化，此过程可被中断，在提交阶段，React 遍历 Effect 列表，将计算出的变化同步应用到 DOM 上，此过程不可中断

- 时间分片：Fiber 将渲染任务分割成多个时间片段，允许中断和复用，避免阻塞主线程
- 优先级分配：Fiber 允许 React 为任务分配不同的优先级，确保优先处理关键的更新
- 错误处理：Fiber 引入新的错误边界概念，使组件能捕获并处理子组件树中的错误
