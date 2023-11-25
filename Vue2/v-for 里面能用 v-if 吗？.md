可以。但官方建议尽量避免在同一元素上同时使用 `v-for` 和 `v-if`

1. 性能：在 vue2 中，`v-for` 的优先级高于 `v-if`。如果你有一个列表和一个条件，Vue 首先会为整个列表创建一个渲染节点，再基于 `v-if` 的条件判断是否显示每一个节点,这可能会导致不必要的渲染和更新
2. 可读性：同时使用 `v-for` 和 `v-if` 会使模板难以阅读和理解

###### 解决方案

如果你确实需要基于某些条件渲染列表中的某些项，可以使用 computed 过滤列表，然后在模板中遍历过滤后的列表

```JavaScript
computed: {
  filteredItems() {
    return this.items.filter(item => ...);
  }
}
```

然后在模板中使用：

```HTML
<div v-for="item in filteredItems" :key="item.id">
  ...
</div>
```
