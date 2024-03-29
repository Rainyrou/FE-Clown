Diff 算法是 Virtual DOM 的核心，用来比较新旧虚拟 DOM 树中节点的差异，并根据差异对真实 DOM 进行最小量更新

- Vue2 的 Diff 算法采用双指针比较新旧虚拟节点的子节点列表。同层级 vnode 进行比较，如果新的 vnode 存在而旧的不存在，则创建新节点；反之，则删除旧节点。在处理子节点时，使用四个指针（旧前、旧后、新前、新后）进行头尾和尾头的比对（即双端 Diff 算法），尽可能地复用现有节点，避免不必要的 DOM 操作（即快速 Diff 算法）
- Vue3 对 Diff 算法进行了优化，增加了更多的静态标记，在编译阶段，Vue3 能够检测并标记出静态的根节点，这些节点在 Diff 过程中可以被直接跳过。对于动态子节点，Vue3 使用 `Map` 来跟踪节点的索引。此外 Vue3 还引入了最长递增子序列算法（其时间复杂度为 O(n log n)）来优化带有 `v-for` 指令的列表渲染，通过计算新旧虚拟节点序列的 LIS，尽可能复用 DOM 元素，并根据差异对真实 DOM 进行最小量更新，因为 LIS 代表无需移动的 DOM 元素集合，只有不在 LIS 中的元素需要移动，这大大减少了布局重排和重新渲染的开销
* React 递归进行同层 vnode 比较，识别差异后将其添加到 `Diff` 队列中，得到一个 patch 树，统一批量操作更新 DOM。如果组件类型或 key 不同，React 直接卸载旧组件并创建新组件。对于列表元素的处理，React 通过比较新旧集合中节点的位置和 `lastIndex` 来判断节点是否需要移动。如果新的 vnode 存在而旧的不存在，则创建新节点；反之，则删除旧节点

###### 判断元素的类型变化

1. 节点的 `tag` 标签名：若两个节点的标签名不同，如一个是`<div>` 另一个是`<span>`，Vue 认为它们是不同类型的元素
2. 节点的 `key` 属性：`key` 用于为列表中的每个节点提供一个唯一标识，如果两个节点的 `tag` 不同但 `key` 不同，Vue 认为它们是不同类型的元素
3. 节点的类型：节点可以是元素节点、文本节点或组件节点等。如果节点的类型不同，Vue 认为它们是不同类型的元素
