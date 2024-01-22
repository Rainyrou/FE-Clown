- Vue2 引入 Option API，所有组件逻辑都放在一个对象里，按照不同选项（如 `data`、`methods`、`computed` 等）组织组件逻辑。复用逻辑需要 mixins 或 HOC组件

* Vue3 引入 Composition API，允许开发者使用 `setup` 函数，根据逻辑关系而非选项类型来组织组件逻辑，即只需将逻辑抽离封装到可复用的函数中，同时它提供了更好的 TypeScript 支持
