###### 1. 响应式原理

- Vue2  响应式的特点是依赖收集，数据可变，自动派发更新，初始化时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`。每个组件实例关联一个 `watcher` 实例，在组件渲染时进行依赖收集，并在依赖项的 `setter` 被调用时通知 `watcher` 重新渲染
- Vue3 使用原生 `Proxy`  重构响应式，`Proxy`  修复 Vue2 响应式存在的缺陷，它可以拦截对象的任意操作（包括增删对象属性、数组索引赋值和修改数组长度等），支持更多数据结构，且不再一开始就递归劫持对象属性，而是代理第一层对象本身，运行时才递归，按需代理，用 `effect`  副作用函数来代替 Vue2  的 `watcher`，用 `trackMap`  代替 Vue2  的 `Dep` 统一管理依赖，无需再维护大量依赖关系，性能显著提升

###### 2. Diff 算法

###### 3. Option API & Composition API

- Vue2 引入 Option API，所有组件逻辑都放在一个对象里，按照不同选项（如 `data`、`methods`、`computed` 等）组织组件逻辑。复用逻辑需要 mixins 或 HOC 组件
* Vue3 引入 Composition API，允许开发者引入 `setup` 函数，使用诸如 `ref` 和 `reactive` 的响应式 API，以及`computed` 和 `watch` 等监听函数，根据逻辑关系而非选项类型来组织组件逻辑，即只需将逻辑抽离封装到可复用的函数中，减少代码冗余并提高代码的可维护性。同时它提供更好的 TypeScript 支持，由于 `setup` 函数直接返回响应式状态和函数，TypeScript 易于推断这些返回值的类型

###### 4. 生命周期钩子

###### 5. TypeScript 支持

- Vue2 对 TypeScript 的支持有限，并不是所有的 Vue2 代码都能很好地与 TypeScript 集成
- Vue3 是用 TypeScript 重写的，它提供更好的 TypeScript 支持

###### 6. Fragment & Teleport & Suspense

###### 7. v-if & v-for & v-model

###### 8. 多个根节点的组件

