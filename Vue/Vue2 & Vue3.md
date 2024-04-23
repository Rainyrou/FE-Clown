###### 1. 响应式原理

- Vue2  响应式的特点是依赖收集，数据可变，自动派发更新，初始化时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`。每个组件实例关联一个 `watcher` 实例，在组件渲染时进行依赖收集，并在依赖项的 `setter` 被调用时通知 `watcher` 重新渲染
- Vue3 使用原生 `Proxy`  重构响应式，`Proxy`  修复 Vue2 响应式存在的缺陷，它可以拦截对象的任意操作（包括增删对象属性、数组索引赋值和修改数组长度等），支持更多数据结构，且不再一开始就递归劫持对象属性，而是代理第一层对象本身，运行时才递归，按需代理，用 `effect`  副作用函数来代替 Vue2  的 `watcher`，用 `trackMap`  代替 Vue2  的 `Dep` 统一管理依赖，无需再维护大量依赖关系，性能显著提升

###### 2. Diff 算法

Diff 算法是 Virtual DOM 的核心，用来比较新旧虚拟 DOM 树中节点的差异，并根据差异对真实 DOM 进行最小量更新

- Vue2 的 Diff 算法采用双指针比较新旧虚拟节点的子节点列表。同层级 vnode 进行比较，如果新的 vnode 存在而旧的不存在，则创建新节点；反之，则删除旧节点。在处理子节点时，使用四个指针（旧前、旧后、新前、新后）进行头尾和尾头的比对（即双端 Diff 算法），尽可能地复用现有节点，避免不必要的 DOM 操作（即快速 Diff 算法）
- Vue3 对 Diff 算法进行了优化，增加了更多的静态标记。在编译阶段，Vue3 能够检测并标记出静态的根节点，这些节点在 Diff 过程中可以被直接跳过。对于动态子节点，Vue3 使用 `Map` 来跟踪节点的索引。Vue3 还引入了最长递增子序列算法（其时间复杂度为O(n log n)）来优化带有 `v-for` 指令的列表渲染，通过计算新旧虚拟节点序列的 LIS，尽可能复用 DOM 元素，并根据差异对真实 DOM 进行最小量更新，因为 LIS 代表无需移动的 DOM 元素集合，只有不在 LIS 中的元素需要移动，这大大减少了布局重排和重新渲染的开销

###### 3. Option API & Composition API

- Vue2 引入 Option API，所有组件逻辑都放在一个对象里，按照不同选项（如 `data`、`methods`、`computed` 等）组织组件逻辑。复用逻辑需要 mixins 或 HOC 组件
* Vue3 引入 Composition API，允许开发者引入 `setup` 函数，使用诸如 `ref` 和 `reactive` 的响应式 API，以及`computed` 和 `watch` 等监听函数，根据逻辑关系而非选项类型来组织组件逻辑，即只需将逻辑抽离封装到可复用的函数中，减少代码冗余并提高代码的可维护性。同时它提供更好的 TypeScript 支持，由于 `setup` 函数直接返回响应式状态和函数，TypeScript 易于推断这些返回值的类型

###### 4. 生命周期钩子

###### 5. TypeScript 支持

- Vue2 对 TypeScript 的支持有限，并不是所有的 Vue2 代码都能很好地与 TypeScript 集成
- Vue3 是用 TypeScript 重写的，它提供了更好的 TypeScript 支持

###### 6. Fragment & Teleport & Suspense

###### 7. v-if & v-for & v-model

###### 8. 多个根节点的组件

