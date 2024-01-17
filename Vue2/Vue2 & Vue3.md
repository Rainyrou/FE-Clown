###### 1. 响应式原理

- Vue2  响应式的特点是依赖收集，数据可变，自动派发更新，初始化时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`。每个组件实例关联一个 `watcher` 实例，在组件渲染时进行依赖收集，并在依赖项的 `setter` 被调用时通知 `watcher` 重新渲染
- Vue3 使用原生 `Proxy`  重构响应式，`Proxy`  修复 Vue2 响应式存在的缺陷，它可以拦截对象的任意操作，支持更多数据结构，且不再一开始就递归劫持对象属性，而是代理第一层对象本身，运行时才递归，按需代理，用 `effect`  副作用来代替 Vue2  里的 `watcher`，用 `trackMap`  代替 Vue2  中的 `Dep` 统一管理依赖，无需再维护大量依赖关系，性能显著提升

###### 2. Diff 算法

- Vue 2 的 diff 算法采用双指针比较新旧虚拟节点的子节点列表。同层级 vnode 进行比较，如果新的 vnode 存在而旧的不存在，则创建新节点；反之，则删除旧节点。在处理子节点时，使用了四个指针（旧前、旧后、新前、新后）进行头尾和尾头的比对，尽可能地复用现有节点，避免不必要的 DOM 操作。移动节点时通过 `splice`  进行数组操作
- Vue 3 对 diff 算法进行了优化，增加了更多的静态标记。在编译阶段，Vue 3 能够检测并标记出静态的根节点，这些节点在 diff 过程中可以被直接跳过。对于动态子节点的比较，Vue 3 使用了 `Map` 数据结构来跟踪节点的索引。Vue 3 还引入了最长递增子序列算法来优化比对过程，它通过比较 `source` 数组记录的位置信息来实现

###### 3. Option API & Composition API

- Vue2 引入 Option API，所有组件逻辑都放在一个对象里，按照不同选项（如 `data`、`methods`、`computed` 等）进行分类

* Vue3 引入 Composition API，允许开发者以更强大且灵活地方式组织组件逻辑

###### 4. TypeScript 支持

- Vue2 对 TypeScript 的支持有限，并不是所有的 Vue2 代码都能很好地与 TypeScript 集成
- Vue3 是用 TypeScript 重写的，它提供了更好的 TypeScript 支持

###### 5. Fragment & Teleport & Suspense

- Vue3 引入了几个新的内置组件，如 Fragment（让组件返回多个根节点）、Teleport（允许将子节点传送到 DOM 的其他部分）和 Suspense（用于异步组件的数据获取和等待）

###### 6. 新的生命周期钩子

- Vue3 引入了几个新的生命周期钩子，如 `onBeforeMount`、`onMounted`、`onBeforeUpdate`、`onUpdated`、`onBeforeUnmount`、`onUnmounted` 等，使得组件的生命周期管理更加直观

###### 6. Tree-shaking

- Vue3 提供了 Tree-shaking 支持，若在项目中没有使用 Vue 的某些功能，它们就不会被打包最终的构建产物，减小包的体积大小

###### 7. 多个根节点的组件

- 在 Vue2 中，每个组件仅有一个根节点

* Vue3 允许有多个根节点
