1. `mounted`：`vm.$el` 替换 `el`，Vue 挂载实例到 DOM 上，在挂载过程中，`watcher` 持续跟踪依赖的变化
2. `updated`：DOM 已更新，可以执行依赖于 DOM 的操作。若此时更新数据，会触发 `beforeUpdate` 和 `updated` 钩子，因此应避免在 `updated` 钩子中更新组件状态
3. `nextTick`：在 JavaScript 的事件循环中，微任务在当前事件循环的末尾，即在浏览器进行下一次重绘之前执行。`$nextTick` 将回调函数放入微任务队列，确保它在所有同步代码执行完毕后才执行，它用于在 DOM 更新完成后立即执行代码，确保其根据最新的响应式数据更新 DOM 元素


