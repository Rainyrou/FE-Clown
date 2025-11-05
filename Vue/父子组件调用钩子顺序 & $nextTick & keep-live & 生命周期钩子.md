父组件 `created` 钩子 -> 父组件 `beforeMount` 钩子 -> 递归遍历所有子组件 -> 子组件 `created` 钩子 -> 子组件 `beforeMount` 钩子 -> 子组件 `mounted` 钩子 -> 父组件 `mounted` 钩子

在 `created` 生命周期钩子中获取 API 接口数据（不依赖 DOM 或 SSR），否则通过 `mounted`

操作 DOM 的钩子：`mounted` + `updated` + `$nextTick`

`$nextTick`：基于事件循环，其将回调置于微任务队列，当修改数据时，视图不立即更新，Vue 将更新任务置于异步更新队列并在下一事件循环中批量处理，在底层上通过 `Promise.then` 和 `MutationObserver` 为微任务队列触发器保证回调在当前事件循环的末尾执行

keep-live：

- 内置组件，其不渲染其 DOM 元素，基于 Map 实现 DOM + 组件实例的双重缓存并在切换时保持原 DOM + 组件实例状态，避免不必要的重新创建和销毁开销
- 组件失活时 `keep-alive` 劫持 `unmount` 钩子并调用  `deactivated`  钩子，将 DOM 和组件实例分别存储于 DOM 缓存池和组件实例缓存，在文档流中移除 DOM，组件激活时劫持 `mounted` 钩子并调用 `activated`  生命周期钩子，通过 key 从 DOM 缓存池和组件实例缓存分别获取 DOM 和组件实例，在文档流中重新插入 DOM
- 支持通过  `max`  属性设置最大缓存数量，通过 LRU 策略淘汰超过限制的缓存，避免内存溢出

Vue 生命周期钩子基于其响应式系统，分为初始化 -> 挂载 -> 更新 -> 销毁阶段

- **setup**: 新增的钩子，在组件创建前执行，用于 Composition API，此时组件的 props 和 reactive state 已可用，但模板和实例尚未挂载和渲染

* **beforeCreate**: 初始化 Vue 实例，进行数据观测
* **created**: 数据绑定、挂载 `computed` 与 `method`、`watch/event` 事件回调，但组件尚未挂载到 DOM，页面尚未渲染，`$el` 仍为 `undefined`
* **beforeMount**: `template` 编译成 `render` 函数，但尚未将渲染结果挂载到 DOM 上，渲染的优先级是 `render` > `template` >  外部 HTML
* **mounted**: `vm.$el` 替换 `el`，Vue 挂载实例到 DOM 上，在挂载过程中，`watcher` 持续跟踪依赖的变化
* **beforeUpdate**: 数据更新时调用，Vue 实例数据已更新，但页面数据还没有更新。若此时更新数据，页面不会重新渲染
* **updated**: DOM 已更新，可以执行依赖于 DOM 的操作。若此时更新数据，会触发 `beforeUpdate` 和 `updated` 钩子，因此应避免在 `updated` 钩子中更新组件状态
* **beforeUnmount**: 实例销毁前调用，此时仍可访问实例属性和方法
* **unmounted**: Vue 实例销毁后调用，此时解绑 Vue 实例绑定的一切，取消实例上所有依赖的追踪，移除所有事件监听器，并销毁所有子实例
* **activated/deactivated**: 用于 `keep-alive` 包裹的动态组件，控制组件的激活与停用
* **errorCaptured**：在捕获后代组件的错误时调用
* **renderTracked**：在组件的依赖项被追踪时被调用
* **renderTriggered**：在组件的依赖项被修改导致组件重新渲染前被调用

![[Pasted image 20231123130223.png]]
