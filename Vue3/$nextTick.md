`$nextTick` 基于事件循环，用于处理 Vue 中的异步更新队列。当你在异步更新队列中修改数据时，视图不会立即更新，Vue 将更新任务放入异步队列中，并在下一个事件循环周期中批量处理，以减少 DOM 操作次数

在 JavaScript 的事件循环中，微任务在当前事件循环的末尾，即在浏览器进行下一次重绘前执行。`$nextTick` 将回调函数放入微任务队列，确保它在所有同步代码执行完毕后才执行，它用于在 DOM 更新完成后立即执行代码，确保其根据最新的响应式数据更新 DOM 元素。Vue 在底层上使用 `Promise.then` 和 `MutationObserver` 作为微任务队列的触发器，若它们不可用则回退到 `setTimeout(fn, 0)`，从而保证回调函数在当前事件循环的末尾执行，此时 DOM 更新已完成

###### 使用场景

1. 当你需要在修改数据后基于最新的 DOM 进行操作时如处理表单元素、计算动态渲染内容的尺寸

如，在一个由 `v-for` 渲染的列表中添加某个元素，由于 Vue 的 DOM 更新是异步的，无法立即获取更新后的列表长度。此时可以在 `$nextTick` 的回调函数中执行相关代码

```JavaScript
methods: {
  addListItem() {
    this.list.push('newElement');
    this.$nextTick(() => console.log('updated length: ', this.$refs.listRef.children.length);
  }
}
```

2. 在 `mounted` 钩子中，DOM 已挂载，但数据还未更新，可以在 `$nextTick` 的回调函数中执行相关代码

```JavaScript
mounted() {
  this.someDataProperty = 'newVal';
  this.$nextTick(() => {});
}
```
