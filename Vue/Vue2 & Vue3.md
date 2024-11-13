```
1. 响应式

2. diff

3. option/composition

4. 生命周期钩子

5. ts 支持

6. v-if/v-for/v-model

7. 多个根节点组件 Fragment + 路由懒加载组件 Suspense + Teleport
```

- Vue2 引入 Option API，所有组件逻辑置于同一对象中，按不同选项组织组件逻辑，复用逻辑通过 mixins 或 HOC
* Vue3 引入 Composition API 和 `setup` 函数，使用 `ref` 和 `reactive` 响应式 API 和 `computed` 和 `watch` 等监听函数，根据逻辑关系而非选项类型来组织组件逻辑，只需将逻辑抽离于可复用函数中，且 TypeScript 兼容性更好，由于 `setup` 函数直接返回响应式状态和函数，TypeScript 易于推断这些返回值的类型
