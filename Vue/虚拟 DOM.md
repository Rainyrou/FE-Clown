虚拟 DOM 是一个 JavaScript 对象，其核心是在 JavaScript 模拟 DOM 树，通过 Diff 算法比较新旧虚拟 DOM 树中节点的差异，并根据差异对真实 DOM 进行最小量更新

1. 重构渲染器
2. 静态树 & 静态属性提升
3. 事件监听器缓存
4. Fragment 支持
5. Block Tree
