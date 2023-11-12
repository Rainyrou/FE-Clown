>[!hint] Helpful hint
>以下说法适用于 Vue2 & Vue3

如果你希望用户在切换组件时保持组件的状态而不是销毁它，你可以使用 `<keep-alive>` 组件来实现缓存组件

`<keep-alive>` 可以保持组件状态，避免重新渲染。当你包裹一个动态组件时，所有的状态都会被保留，即组件不会被销毁和重新创建

```JavaScript
<keep-alive>
  <component :is="currentComponent"></component>
</keep-alive>
```

在上面的代码中，`currentComponent` 是一个动态绑定的组件，根据需要展示不同的组件。只有当 `currentComponent` 变化时，Vue 才会切换组件

被 `<keep-alive>` 包裹的组件会多出两个生命周期钩子：`activated` 和 `deactivated`。当组件被激活时调用 `activated`，当组件被停用时调用 `deactivated`

```JavaScript
export default {
  activated() {
    console.log('Component is activated');
  },
  deactivated() {
    console.log('Component is deactivated');
  }
};
```

`<keep-alive>` 本身提供了两个属性来进行更细粒度的控制：

- `include`：字符串或正则表达式。只有名称匹配的组件会被缓存
- `exclude`：字符串或正则表达式。任何名称匹配的组件都不会被缓存

```JavaScript
<keep-alive :include="whiteList">
  <component :is="currentComponent"></component>
</keep-alive>
```

在这个例子中，只有 `whiteList` 中包含的组件名称才会被 `<keep-alive>` 缓存

###### 底层原理

在底层，Vue 使用了一个缓存对象来存储和复用组件实例。当一个组件被 `<keep-alive>` 包裹时，如果该组件没有被缓存，则创建一个新的组件实例；如果该组件已经被缓存，则从缓存中取出并复用

在源码层面，`<keep-alive>` 组件的渲染函数会检查其插槽内容的组件节点，并决定是创建一个新的 VNode 实例还是从缓存中取出一个实例。组件的实例会保留在内存中，直到它被排除或 `<keep-alive>` 被销毁

使用 `<keep-alive>` 会占用内存。因此避免缓存大量组件，特别是那些占用大量内存或需要频繁更新的组件


