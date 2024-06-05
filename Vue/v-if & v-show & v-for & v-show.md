优先级：`v-for` > `v-if` > `v-show`（Vue2）`v-if` > `v-for` > `v-show`（Vue3）
`v-if` 指令用于根据表达式真假渲染元素到 DOM 上
`v-show` 指令用于根据表达式真假切换元素的可见性，无论表达式真假，与 `v-show` 相应的 DOM 元素始终渲染，只切换其 CSS `display` 属性

`v-model` 本质上为实现 Vue 组件的双向数据绑定，将组件内部状态与外部绑定的数据同步，从而在组件内外均反映同一值。在 Vue2 中 `v-model` 默认绑定到 `value` 属性并通过 `input` 事件进行更新，而在 Vue3 中，默认属性和事件为可配置，这使得 `v-model` 可绑定到不同的属性和事件上。Vue3 支持多 `v-model` 绑定，一个组件可以有多个 `v-model` 绑定不同属性如 `v-model:title="title" v-model:content="content"`

Vue 解析模板时，查找 `v-model` 指令的目标属性，在组件实例上创建 `props` 属性，绑定父组件传入的值如 `modelValue`，同时在组件实例上注册事件监听器，监听 `update:modelValue` 事件，当组件内部状态变化时如用户输入新值，组件通过 `$emit('update:modelValue', newValue)` 触发事件，父组件接收到 `update:modelValue` 事件，并更新其状态


