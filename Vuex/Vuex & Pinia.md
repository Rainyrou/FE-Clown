Vuex 和 Pinia 都是状态管理库，用于管理 Vue 应用的全局状态。它们都基于 Vue 响应式系统来追踪对状态的访问和修改，并触发视图更新

###### Vuex

Vuex 采用了基于 Flux 架构的思想：

- 使用单一状态树，所有组件的状态都存储在一个全局对象中
- 将状态树分割成 module，每个模块可以拥有自己的 state、getter、mutation 和 action
- 所有的状态修改都必须通过同步的 mutations 进行，而所有异步逻辑必须封装在 actions 中
- 支持插件，允许在状态管理过程中插入自定义逻辑

###### Pinia

Pinia 被认为是 Vuex 的精神继承者，旨在提供更简单、直观的 API，同时保留 Vuex 的核心概念：

- 与 TypeScript 兼容，它提供更好的类型推断，以及补全代码的功能
- 足够轻量，压缩后的体积只有 1KB 左右
- 代码扁平化没有模块嵌套，将状态管理分割成 store，每个 store 都是一个独立的响应式对象，无需手动添加 store
- 去除 mutations，只有 state、getters、actions
- actions 支持同步和异步
