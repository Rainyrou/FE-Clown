`v-if` 用于根据表达式真假渲染元素到 DOM 上
`v-show` 用于根据表达式真假切换元素的可见性，无论表达式真假，与 `v-show` 相应的 DOM 元素始终渲染，只切换其 CSS `display` 属性

Vue2：`v-for` 优先级高于 `v-if` 即 `v-for` 先遍历列表项，生成所有 DOM 节点，即便某些节点最后是被过滤掉的，`v-if` 对已生成的各个虚拟 DOM 节点进行条件判断，虽符合预期，但增加不必要的性能开销
Vue3：`v-if` 优先级高于 `v-for` 即 `v-if` 先进行条件判断，只有条件成立时才进行 `v-for` 的渲染，若不满足条件则直接跳过该列表项的渲染，避免生成不必要的虚拟 DOM 节点

本质在于 Vue3 虚拟 DOM 优化及底层的渲染算法，最佳实践是在 Vue2 和 Vue3 中尽可能避免将 `v-if` 和 `v-for` 放在同一元素上，而是通过 `computed` 或 `filter` 等方法处理数据

`v-model` 本质上为实现 Vue 组件的双向数据绑定，将组件内部状态与外部绑定的数据同步，从而在组件内外均反映同一值，在 Vue2 中 `v-model` 默认绑定到 `value` 属性并通过 `input` 事件进行更新，而在 Vue3 中，默认属性和事件为可配置，这使得 `v-model` 可绑定到不同属性和事件上即支持多 `v-model` 绑定，一个组件可以有多个 `v-model` 绑定不同属性如 `v-model:title="title" v-model:content="content"`

Vue 解析模板时，查找 `v-model` 指令的目标属性，在组件实例上创建 `props` 属性，绑定父组件传入的值如 `modelValue`，同时在组件实例上注册事件监听器，监听 `update:modelValue` 事件，当组件内部状态变化时如用户输入新值，组件通过 `$emit('update:modelValue', newValue)` 触发事件，父组件接收到 `update:modelValue` 事件并更新其状态
