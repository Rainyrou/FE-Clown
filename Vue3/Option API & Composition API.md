- Vue2 引入 Option API，所有组件逻辑都放在一个对象里，按照不同选项（如 `data`、`methods`、`computed` 等）组织组件逻辑。复用逻辑需要 mixins 或 HOC 组件
* Vue3 引入 Composition API，允许开发者引入 `setup` 函数，使用诸如 `ref` 和 `reactive` 的响应式 API，以及`computed` 和 `watch` 等监听函数，根据逻辑关系而非选项类型来组织组件逻辑，即只需将逻辑抽离封装到可复用的函数中，减少代码冗余并提高代码的可维护性。同时它提供了更好的 TypeScript 支持，由于 `setup` 函数直接返回响应式状态和函数，TypeScript 易于推断这些返回值的类型
* 对于熟悉 Options API 的开发者来说，Composition API 引入新的概念和 API，增加学习成本，开发者需要理解 `ref`、`reactive` 等响应式 API 的使用方式，以及如何在 `setup` 函数中组织组件逻辑。此外，若不加以限制地使用 Composition API，特别是在一个组件中过度使用自定义逻辑和复杂的 Composition 函数，可能使得组件的结构和逻辑变得难以理解。还有，虽然 Vue 3 支持 Options API 和 Composition API 共存，但完全利用 Composition API 的新特性需要对现有代码进行重构，带来不小的迁移成本

