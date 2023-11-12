可以。但官方建议尽量避免在同一元素上同时使用 `v-for` 和 `v-if`

1. 性能考虑：当 Vue 处理一个元素的指令时，`v-for` 的优先级高于 `v-if`。这意味着，如果你有一个列表和一个条件，Vue 首先会为整个列表创建一个渲染节点，然后再基于 `v-if` 的条件检查是否应该显示每一个节点。这可能会导致不必要的渲染和更新
    
2. 可读性考虑：同时使用 `v-for` 和 `v-if` 可能会使模板难以阅读和理解。考虑到代码的清晰度和可维护性，最好避免在同一个元素上混用这两个指令

###### 解决方案

如果你需要基于某些条件渲染列表中的某些项，可以使用计算属性先过滤列表，然后在模板中遍历过滤后的列表

```JavaScript
computed: {
  filteredItems() {
    return this.items.filter(item => /* your condition here */);
  }
}
```

然后在模板中使用：

```HTML
<div v-for="item in filteredItems" :key="item.id">
  <!-- content -->
</div>
```

