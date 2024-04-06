`$nextTick` 基于事件循环，用于处理 Vue 中的异步更新队列。当你在异步更新队列中修改数据时，视图不会立即更新，Vue 将更新任务放入异步队列中，并在下一个事件循环周期中批量处理，以减少 DOM 操作次数

在 JavaScript 的事件循环中，微任务在当前事件循环的末尾，即在浏览器进行下一次重绘前执行。`$nextTick` 将回调函数放入微任务队列，确保它在所有同步代码执行完毕后才执行，它用于在 DOM 更新完成后立即执行代码，确保其根据最新的响应式数据更新 DOM 元素。Vue 在底层上使用 `Promise.then` 和 `MutationObserver` 作为微任务队列的触发器，`MutationObserver` 用于监听 DOM 变化，若浏览器不支持，则回退到 `setTimeout(fn, 0)`，从而保证回调函数在当前事件循环的末尾执行，此时 DOM 更新已完成

###### 使用场景

1. 在 DOM 更新完成后立即执行代码，确保其根据最新的响应式数据更新 DOM 元素

```JavaScript
methods: {
  addListItem() {
    this.list.push('newElement');
    this.$nextTick(() => console.log('updated length: ', this.$refs.listRef.children.length);
  }
}
```

2. `mounted` 钩子的 DOM 已挂载，但数据尚未更新，可在 `$nextTick` 的回调函数中执行相关代码

```JavaScript
mounted() {
  this.someDataProperty = 'newVal';
  this.$nextTick(() => {});
}
```
