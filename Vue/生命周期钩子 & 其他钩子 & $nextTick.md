Vue 生命周期钩子是基于其响应式系统实现的

初始化 -> 挂载 -> 更新 -> 销毁

在 `mounted` 生命周期钩子中发起请求更为合适而非 `created`，组件渲染依赖于虚拟 DOM，组件在 `created` 生命周期钩子中并未挂载到 DOM 上，此时发起异步请求将导致请求与页面初始渲染同时进行，请求返回的数据可能影响页面渲染

父组件 `created` 钩子 -> 父组件 `beforeMount` 钩子 -> 递归遍历所有子组件 -> 子组件 `created` 钩子 -> 子组件 `beforeMount` 钩子 -> 子组件 `mounted` 钩子 -> 父组件 `mounted` 钩子

操作 DOM 的钩子：`mounted` + `updated` + `$nextTick`

`$nextTick` 基于事件循环，用于处理 Vue 中的异步更新队列。当你在异步更新队列中修改数据时，视图不会立即更新，Vue 将更新任务放入异步队列中，并在下一个事件循环周期中批量处理，以减少 DOM 操作次数

在 JavaScript 的事件循环中，微任务在当前事件循环的末尾，即在浏览器进行下一次重绘前执行。`$nextTick` 将回调函数放入微任务队列，确保它在所有同步代码执行完毕后才执行，它用于在 DOM 更新完成后立即执行代码，确保其根据最新的响应式数据更新 DOM 元素。Vue 在底层上使用 `Promise.then` 和 `MutationObserver` 作为微任务队列的触发器，`MutationObserver` 用于监听 DOM 变化，若浏览器不支持，则回退到 `setTimeout(fn, 0)`，从而保证回调函数在当前事件循环的末尾执行，此时 DOM 更新已完成

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
