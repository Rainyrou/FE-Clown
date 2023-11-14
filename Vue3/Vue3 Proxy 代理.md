在 Vue 3 中，`Proxy` 对象用于创建响应式数据。它是 Vue 3 响应式原理的核心，替代了 Vue 2 中的 `Object.defineProperty` 方法。`Proxy` 能够拦截和监视对象的多种操作，这在实现 Vue 3 的响应式原理中发挥了关键性作用

`Proxy` 可以拦截以下类型的操作：

1. 定义属性 `Object.defineProperty`
2. 获取属性 `get`
3. 设置属性 `set`
4. 删除属性 `deleteProperty`
5. 检查属性 `has`
6. 获取对象的自有属性键 `Object.getOwnPropertyNames`
7. 获取属性描述符 `Object.getOwnPropertyDescriptor`
8. 获取原型 `Object.getPrototypeOf``
9. 设置原型 `Object.setPrototypeOf`
10. 调用函数 `apply`

Vue3 使用 `Proxy` 相比于 Vue 2 使用 `Object.defineProperty` 的优势：

- `Proxy` 可以代理整个对象，无需递归逐个属性设置
- `Proxy` 可以拦截更多操作，包括增删属性、修改数组索引赋值和数组长度等
- `Proxy` 提供更好的性能，特别是在处理大型对象或数组时

###### 底层原理

- 当一个对象被代理后，任何对该对象的操作都会被 `Proxy` 拦截
- Vue 3 响应式系统在这些拦截操作中维护依赖关系（依赖收集和触发更新）
- 当对象的属性被读取时，Vue 3 会将当前活跃的响应式效果（如渲染函数或计算属性）添加到该属性的依赖列表中
- 当对象的属性被修改时，触发该属性的依赖，导致视图更新或计算属性重新计算

