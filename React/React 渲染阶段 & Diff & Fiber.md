React 组件渲染为 JSX → JavaScript → 虚拟 DOM → 真实 DOM，基于 Fiber 架构拆分为调度 → 协调 → 提交 → 卸载阶段

1. 调度阶段：根据渲染任务优先级排序，通过类似 `MessageChannel`  的机制实现时间切片，每次只执行一个 Fiber 节点的计算，剩余任务置于下一时间分片

2. 协调阶段：Diff 算法 + Fiber 架构

传统查找任意两颗树间的最小编辑距离时间复杂度为 $O(N^3)$，React 只进行同层级 vnode 比较，若节点类型（DOM 标签或组件类型）不同，直接销毁旧节点、创建新节点，再通过 `key` 唯一标识比较节点，若 `key` 相同则复用该节点，递归比较其属性和子节点，已存在的节点根据相对位置移动，否则销毁旧节点、创建新节点；通过 Fiber 架构的双缓冲渲染模型（current tree + workInProgress tree） + Map 存储 vnode 和 `key` + 副作用列表存储 DOM 的增删修改操作，因此 React Diff 的时间复杂度为 $O(N)$，空间复杂度也是 $O(N)$

在引入 Fiber 架构前，React 通过堆栈渲染器的同步渲染引擎渲染组件，React 递归遍历组件树，对需更新的组件调用 `render` 方法，比较上一次的渲染结果以更新 DOM，此种更新机制是同步且一次性完成的，一旦开始就无法中断，无法为不同渲染任务分配不同优先级且较大组件树的复杂更新阻塞浏览器主线程

React Fiber 为解决上述问题而生的，其重构 React Virtual DOM，各个 React 元素对应一至多个 Fiber 节点，其构成一个工作单元的树形结构，各个 Fiber 节点包含父子兄弟指针，将递归遍历转换为迭代遍历，React 为各个 Fiber 节点标记 Effect 类型并拼接为副作用链表，在底层上通过类似 `requestIdleCallback/MessageChannel` 的机制检测浏览器主线程的空闲时间，调度器内部维护任务队列，各个任务均有其到期时间，React 实现任务分割和优先级排序并据此顺序执行它们，在更新过程中中断和复用渲染任务（记录当前节点指针，下次恢复时直接从该节点继续遍历），确保优先处理关键渲染任务

3. 提交阶段

- beforeMutation 阶段执行 DOM 操作前的准备工作如读取 DOM 节点的布局信息和调用`getSnapshotBeforeUpdate/useLayoutEffect`
- mutation 阶段遍历副作用链表，根据 Fiber 节点的 Effect 标记执行相应的 DOM 操作
- layout 阶段执行 DOM 操作后的收尾工作如更新 Fiber 树的引用，将 workInProgress tree 切换为 current tree、调用  `componentDidMount/componentDidUpdate/useLayoutEffect`

4. 卸载阶段：移除真实 DOM 节点 + 副作用 + 触发 `componentWillUnmount/useEffect` 清理函数
