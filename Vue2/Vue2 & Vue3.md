###### 1. 性能提升

- Vue3 提供了更好的性能，特别是在虚拟 DOM 重写和树的更新算法方面。Vue3 通过使用 `Proxy` 代理而不是`Object.defineProperty` 实现了更快的组件初始化和更新。它比 Vue2 快 20%，且内存减半

###### 2. 组合式 API

- Vue3 引入了 Composition API，允许开发者更灵活地组织组件逻辑。而 Vue2 使用的是 Option API，所有的组件逻辑都放在一个对象里，按照不同的选项（如 `data`、`methods`、`computed` 等）分类

###### 3. 响应式系统

- Vue3 使用 `Proxy` 作为其响应式系统的基础，不仅改善了性能，还增加了对集合类型的响应式支持，如 `Map` 和 `Set`。
- Vue2 使用 `Object.defineProperty` 作为其响应式系统的基础，这限制了某些响应式操作，且不支持集合类型的响应式变化

###### 4. TypeScript 支持

- Vue3 是用 TypeScript 编写的，因此它提供了更好的 TypeScript 支持
- Vue2 对 TypeScript 的支持有限，虽然它也可以使用 TypeScript 编写，但并不是所有的 Vue2 代码基都能很好地与 TypeScript 集成

###### 5. Fragment & Teleport & Suspense

- Vue3 引入了几个新的内置组件，比如 Fragment（让组件返回多个根节点）、Teleport（允许将子节点传送到 DOM 的其他部分）和 Suspense（用于异步组件的数据获取和等待）

###### 6. Tree-shaking

- Vue3 提供了 Tree-shaking 支持，如果你没有使用 Vue 的某些功能，它们就不会被打包到最终的构建中，从而减小了包的大小

###### 7. 自定义渲染器 API

- Vue3 提供了创建自定义渲染器的 API，使得 Vue 更容易地与不同平台集成，如原生移动开发或小程序

###### 8. 更详细的警告和错误堆栈

- Vue3 在开发模式下提供了更详细的警告和错误堆栈，以帮助开发者定位问题

###### 9. 更灵活的组件实例

- Vue3 允许开发者使用 `setup()` 函数访问组件实例，这提供了更多操作组件实例的灵活性

###### 10. 多个根节点的组件

- Vue3 允许 SFC 有多个根节点，而在 Vue2 中，每个组件只能有一个根节点

###### 11. V-model 的变化

- 在 Vue3 中，`v-model` 语法更加灵活，支持自定义事件名称和修饰符，这让它在处理表单时更加强大
- 相比之下，Vue2 的 `v-model` 是固定的，主要用于简单的双向数据绑定

###### 12. 组件的 v-if 优先级

- Vue3 改变了模板中的 `v-if` 和 `v-for` 的优先级，`v-if` 现在有比 `v-for` 更高的优先级

###### 13. 更多的动画和过渡选项

- Vue3 对动画和过渡的支持进行了增强，提供了更多选项和钩子，使得动画实现更加丰富和可控

###### 14. 新的生命周期钩子

- Vue3 引入了几个新的生命周期钩子，如 `onBeforeMount`、`onMounted`、`onBeforeUpdate`、`onUpdated`、`onBeforeUnmount`、`onUnmounted` 等，这使得组件的生命周期管理更加直观和易于理解
