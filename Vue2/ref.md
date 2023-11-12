`ref` 被用来给元素或子组件注册引用信息，引用信息将会注册在父组件的 `$refs` 对象上。如果用在普通的 DOM 元素上，引用指向 DOM 元素；如果用在子组件上，引用指向组件实例：

```html
<!-- `vm.$refs.p` will be the DOM node -->
<p ref="p">hello</p>

<!-- `vm.$refs.child` will be the child component instance -->
<child-component ref="child"></child-component>
```

通过 `this.$refs.p` 访问到 `<p>` 元素；通过 `this.$refs.child` 访问到 `<child-component>` 组件的实例

- 当 `v-for` 用于元素或组件的时候，引用信息将是包含 DOM 节点或组件实例的数组
- ref 本身作为渲染结果被创建，在初始渲染时你不能访问它们
- `$refs` 不是响应式的，因此不应在模板中用它做数据绑定

###### 访问子组件或子元素

尽管存在 prop 和事件，有时你仍可能需要在 JavaScript 里直接访问一个子组件。为达到这个目的，你可以通过 `ref` 这个 attribute 为子组件赋予一个 ID 引用

```html
<base-input ref="usernameInput"/> 
```

现在你在已经定义了这个 `ref` 的组件库里，可以使用 `this.$refs.usernameInput` 来访问这个 `<base-input>` 实例，比如程序化地从一个父级组件聚焦该输入框。在 `<base-input>` 组件内部，你也可以定义一个 `ref` 属性来提供对内部元素的引用：

```html
<template>
	<div>
		<input ref="input">
	</div>
</template>
```

在这个例子中，`ref="input"` 创建了一个引用到 `<input>` 的元素。在这个组件的方法中，可通过 `this.$refs.input` 来访问这个 `<input>` 元素

可以在 `<base-input>` 组件内部定义一个方法 `focus`，此方法会聚焦到内部的 `<input>` 元素：

```JavaScript
methods: {
	focus: function() {
		this.$refs.input.focus()
	}
}
```

`this.$refs.input` 就是 `<input>` 元素的 DOM 对象，所以 `this.$refs.input.focus()` 相当于直接对 `<input>` 元素调用 `focus` 方法

在父级组件中，可通过调用这个 `focus` 方法来聚焦 `<base-input>` 组件内部的 `<input>` 元素：

```JavaScript
this.$refs.usernameInput.focus()
```

在这里，`this.$refs.usernameInput` 是 `<base-input>` 组件的实例，所以 `this.$refs.usernameInput.focus()` 就相当于直接在 `<base-input>` 组件实例上调用 `focus` 方法


