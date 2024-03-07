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


```TypeScript
<component :is="activeComponent" />
```

默认情况下，一个组件实例在被替换掉后会被销毁。这会导致它丢失其中所有已变化的状态——当这个组件再一次被显示时，会创建一个只带有初始状态的新实例

在切换时创建新的组件实例通常是有意义的，但在这个例子中，我们的确想要组件能在被"切走"的时候保留它们的状态。要解决这个问题，我们可以用  `<KeepAlive>`  内置组件将这些动态组件包装起来：

```TypeScript
<KeepAlive>
	<component :is="activeComponent">
</KeepAlive>
```

当你将一个组件放入 `<KeepAlive>` 标签内时，这个组件不会按照常规方式进行销毁。取而代之的是，它会被放入一个缓存池中。当组件再次被激活时，它将从缓存池中恢复，而不是从头开始创建。这意味着所有在组件被缓存期间发生的状态变更都会被保存并在下次激活时保留

`<KeepAlive>`  默认会缓存内部的所有组件实例，但我们可以通过  `include`  和  `exclude` prop 来定制该行为。这两个 prop 的值都可以是一个以英文逗号分隔的字符串、一个正则表达式，或是包含这两种类型的一个数组：

```TypeScript
<KeepAlive include="a,b">
	<component :is="view" />
</KeepAlive>
<KeepAlive :include="/a|b/">
	<component :is="view" />
</KeepAlive>
<KeepAlive :include="['a', 'b']">
	<component :is="view" />
</KeepAlive>
```

它会根据组件的 `name` 选项进行匹配，因此如果一个组件要被 `<KeepAlive>` 缓存或排除，就必须显式声明一个  `name`  选项

在 3.2.34 或以上的版本中，使用  `<script setup>`  的单文件组件会自动根据文件名生成对应的  `name`  选项，无需再手动声明

我们可以通过传入  `max` prop 来限制可被缓存的最大组件实例数。`<KeepAlive>`  的行为在指定了  `max`  后类似一个  LRU 缓存：如果缓存的实例数量即将超过指定的最大数量，则最久没有被访问的缓存实例将被销毁，以便为新的实例腾出空间

```TypeScript
<KeepAlive>
	<component :is="activeComponent" />
</KeepAlive>
```

###### 缓存实例的生命周期

当一个组件实例从 DOM 上移除但因为被  `<KeepAlive>`  缓存而仍作为组件树的一部分时，它将变为不活跃状态而不是被卸载。当一个组件实例作为缓存树的一部分插入到 DOM 中时，它将被重新激活

一个持续存在的组件可以通过  `activated`  和  `deactivated`  选项来注册相应的两个状态的生命周期钩子：

最后，请注意，这两个钩子不仅仅适用于被 `<KeepAlive>` 直接包裹的组件，也适用于这个组件的任何子组件。这意味着，如果一个父组件被 `<KeepAlive>` 缓存了，它的所有子组件都会经历 `activated` 和 `deactivated` 钩子

`activated` 在组件挂载及每次从缓存中被重新插入的时候调用，`deactivated` 在从 DOM 上移除、进入缓存及组件卸载时调用

###### 底层原理

在底层，Vue 使用了一个缓存对象来存储和复用组件实例。当一个组件被 `<keep-alive>` 包裹时，如果该组件没有被缓存，则创建一个新的组件实例；如果该组件已经被缓存，则从缓存中取出并复用

在源码层面，`<keep-alive>` 组件的渲染函数会检查其插槽内容的组件节点，并决定是创建一个新的 VNode 实例还是从缓存中取出一个实例。组件的实例会保留在内存中，直到它被排除或 `<keep-alive>` 被销毁

使用 `<keep-alive>` 会占用内存。因此避免缓存大量组件，特别是那些占用大量内存或需要频繁更新的组件

`<KeepAlive>`  是一个内置组件，它的功能是在多个组件间动态切换时缓存被移除的组件实例

