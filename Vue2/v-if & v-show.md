`v-if` 和 `v-show` 用于根据条件控制元素的显示

##### v-if

###### 特点

1. 条件性渲染：当条件不满足时，与 `v-if` 相关的 DOM 元素不会被渲染到页面上
2. 惰性渲染：如果在初始渲染时条件为假，则什么也不做——即使绑定的数据后来改变为真，相关的 DOM 也不会被渲染
3. 性能开销：对于需要频繁切换显示状态的元素，`v-if` 不是最佳选择，因为涉及到频繁地创建和销毁 DOM 元素

```JavaScript
<div v-if="isVisible">visible</div>
```

##### v-show

###### 特点

1. 始终渲染：无论条件真假，与 `v-show` 相关的 DOM 元素始终会被渲染
2. CSS 切换：`v-show` 只是切换元素的 CSS 属性 `display`
3. 性能开销：对于需要频繁切换显示状态的元素，`v-show` 更高效，因为不涉及到创建和销毁 DOM 元素

```JavaScript
<div v-show="isVisible">visible</div>
```
