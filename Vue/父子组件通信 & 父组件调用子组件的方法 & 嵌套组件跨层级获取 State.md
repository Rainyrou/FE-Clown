父子组件通信：

1. `props` & `$emit`：父组件通过 `props` 向子组件传递数据，子组件通过声明 `props` 接收父组件数据，子组件通过 `$emit` 触发自定义事件，以数据为参数传递给该事件，父组件监听该事件并定义相应逻辑
2. `$children` & `$parent`：通过 `$children` & `$parent` 访问父子组件的属性和方法
3. `ref` & `$refs`：父组件通过 `ref` 为子组件提供引用 ID，通过 `$refs` 访问子组件的属性和方法
4. `provide` & `inject`：父组件通过 `provide` 提供变量，子组件通过 `inject` 注入变量
5. `eventBus`：以空 Vue 实例为 eventBus，组件通过 eventBus 触发和监听事件
6. `Vuex/Pinia`

嵌套组件（A → B → C）跨层级获取 State：Props 透传 + Context 注入 + 全局状态库 + 组件组合（组件 A 将组件 C 作为 `children` 或插槽传递给组件 B，组件 A 将组件 A 和 B 的 State 作为 Props 传给实例化的组件 C）
