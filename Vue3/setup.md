`setup` 函数是 Composition API 的入口点，它为组件逻辑提供一个更加灵活的组织方式。 `setup` 函数接收 `props` 和 `context` 作为参数，使我们能够访问组件的属性和上下文，通过 `ref` 和 `reactive` 等 API 定义组件的响应式状态，通过 `computed` 和 `watch` 等 API 定义计算属性和响应式监听器，注册生命周期钩子，其不可使用 `this` 来访问组件实例，在执行 `setup` 函数时，组件实例尚未创建，`this` 在 `setup` 函数中是 `undefined`。这是 Vue3 设计上的一部分，减少对 `this` 上下文的依赖，旨在鼓励我们不依赖于组件实例，有利于结合 TypeScript

