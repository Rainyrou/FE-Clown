`$set` 是一个全局 API，用于在响应式对象上动态添加新属性，同时确保新属性是响应式的

初始化 Vue2 实例或组件时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`。每个组件实例关联一个 `watcher` 实例，在组件渲染时进行依赖收集，并在依赖项的 `setter` 被调用时通知 `watcher` 重新渲染

但若你在初始化实例后增删对象的属性，该属性不会自动变成响应式的，即没有上述过程，这也是 Vue2 响应式的缺陷。`$set` 能解决此问题

```JavaScript
this.$set(object, propertyName, value);
```

依次是要增加属性的对象、属性名和属性值

##### 底层原理

底层是对 `Vue.set` 或 `vm.$set` 的封装。当你调用 `$set` 时：

1. Vue 检查是否为响应式对象，如果不是，直接赋值返回
2. 检查对象中的属性是否为响应式的，如果是，直接赋值
3. 若属性不存在，使用 `Object.defineProperty` 执行实现 Vue2 响应式原理的类似过程
