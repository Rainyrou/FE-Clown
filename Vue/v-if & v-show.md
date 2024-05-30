##### v-if

`v-if` 指令用于根据表达式真假条件性渲染元素到 DOM 上

###### 特点

1. 条件性渲染：当条件不满足时，与 `v-if` 相关的 DOM 元素不会被渲染到页面上
2. 惰性渲染：如果在初始渲染时条件为假，则什么也不做——即使绑定的数据后来改变为真，相关的 DOM 也不会被渲染
3. 性能开销：对于需要频繁切换显示状态的元素，`v-if` 不是最佳选择，因为涉及到频繁地创建和销毁 DOM 元素

基本用法：

```JavaScript
<div v-if="isVisible">visible</div>
```

###### 底层原理

1. 编译阶段：Vue 模板编译成渲染函数，渲染函数使用 `h` 函数即 `createElement` 函数来创建虚拟 DOM，`v-if` 指令被转换成 JavaScript 条件语句，根据条件的结果来决定是否创建相应的虚拟节点
2. 响应式系统：`v-if` 指令绑定一个响应式数据。当数据变化时，Vue 响应式系统重新执行渲染函数来更新虚拟 DOM。如果条件从 `false` 变为 `true`，Vue 创建新元素并插入到 DOM 中。如果条件从 `true` 变为 `false`，Vue 从 DOM 中移除相应元素
3. 优化：处理 `v-if` 时，Vue 尽可能地复用现有的元素，而非盲目销毁旧元素并创建新元素。对于同时使用 `v-if`和 `v-else` 或 `v-else-if` 的情况，Vue 将这些条件分支视为一个整体，以确保在条件变化时只有一个分支被渲染

##### v-show

`v-show` 指令用于根据表达式真假切换元素的可见性，与 `v-if` 不同，`v-show` 不会将元素从 DOM 中添加或移除，它仅仅切换元素的 CSS `display` 属性

###### 特点

1. 始终渲染：无论条件真假，与 `v-show` 相关的 DOM 元素始终会被渲染
2. CSS 切换：`v-show` 只是切换元素的 CSS 属性 `display`
3. 性能开销：对于需要频繁切换显示状态的元素，`v-show` 更高效，因为不涉及到创建和销毁 DOM 元素

基本用法：

```JavaScript
<div v-show="isVisible">visible</div>
```

###### 底层原理

```JavaScript
const vShow = {
    beforeMount(el, { value }) {
        el.initialDisplay = el.style.display === 'none' ? '' : el.style.display;
        el.style.display = value ? el.initialDisplay : 'none'
    },
    updated(el, { value, oldValue }) {
        if(value !== oldValue) el.style.display = value ? el.initialDisplay : 'none';
    }
}

app.directive('show', vShow);
```

在模板中使用：

```JavaScript
<div v-show="isVisible">show content</div>
```
