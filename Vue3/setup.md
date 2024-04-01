`setup` 函数是 Composition API 的入口点，它为组件的逻辑和状态提供一个更加灵活的组织方式。此外 `setup` 函数中不可以使用 `this` 来访问组件实例，在执行 `setup` 函数时，组件实例还没有被创建，因此 `this` 在 `setup` 函数中是 `undefined`。这是 Vue3 设计上的一部分，减少对 `this` 上下文的依赖，旨在鼓励我们使用 `ref`、`reactive` 等函数来声明响应式状态，而不是依赖于组件实例，有利于与 TypeScript 结合使用，提高代码的可维护性

###### 用途

1. `setup` 函数接收 `props` 和 `context` 作为参数，使我们能够访问组件的属性和上下文
2. 在 `setup` 内部通过 `ref` 和 `reactive` 等 API 定义组件的响应式状态
3. 通过 `computed` 和 `watch` 等 API 定义计算属性和响应式监听器
4. 注册生命周期钩子
5. 允许我们创建可复用的响应式函数即组合函数

###### this 指向的替代方案

- `props` 和 `context`：`setup` 函数接收 `props` 和 `context` 作为参数，`props` 是组件传入的 props，而 `context`是一个 JavaScript 对象，包含组件的 `attrs`、`slots` 和 `emit` 方法，使我们能够访问组件的属性和上下文
- 使用 Composition API：通过 `ref`、`reactive`、`computed` 和 `watch` 等 Composition API 定义组件的响应式状态、计算属性和响应式监听器，无需依赖 `this`
