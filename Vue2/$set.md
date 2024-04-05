初始化时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`。每个组件实例关联一个 `watcher` 实例，在组件渲染时进行依赖收集，并在依赖项的 `setter` 被调用时通知 `watcher` 重新渲染，但Vue2响应式的一大缺陷是增删对象属性后，该属性不会自动变成响应式，`$set` 为解决上述问题而产生，其用于在响应式对象上动态添加新属性，同时确保新属性是响应式的。`$set` 在底层上，Vue首先检查对象是否为响应式，若是，直接赋值返回，接着检查对象属性是否为响应式，若是，直接赋值返回，若属性不存在，使用 `Object.defineProperty` 实现响应式原理

```JavaScript
this.$set(object, propertyName, value);
```

依次为增加的属性对象、属性名和属性值




