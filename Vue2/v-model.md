你可以用  `v-model`  指令在表单  `<input>`、`<textarea>`  及  `<select>`  元素上创建双向数据绑定，它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但  `v-model`  本质上不过是语法糖。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理

`v-model`  会忽略所有表单元素的  `value`、`checked`、`selected` attribute 的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的  `data`  选项中声明初始值

`v-model`  在内部为不同的输入元素使用不同的 property 并抛出不同的事件：

- text 和 textarea 元素使用  `value` property 和  `input`  事件
- checkbox 和 radio 使用  `checked` property 和  `change`  事件
- select 字段将  `value`  作为 prop 并将  `change`  作为事件

```html
<select v-model="selected">
	<option v-for="option in options" v-bind:value="option.value">
		{{ option.text }}
	</option>
</select>
<span>Selected: {{ selected }}</span>
```

```JavaScript
new Vue({
	el: '...',
	data: {
		selected: 'A',
		options: [
			{ text: 'One', value: 'A' },
			{ text: 'Two', value: 'B' },
			{ text: 'Three', value: 'C' }
		]
	}
})
```

`v-model="selected"` 等同于同时设置 `v-bind:value="selected"` 和 `v-on:input="selected = $event.target.value"`

###### 修饰符

- 默认情况下，在每次 `input` 事件触发后 v-model 将输入框的值与数据进行同步（除了输入法组合文字外），你可以添加 `lazy` 修饰符，从而转为在 `change` 事件之后进行同步：

```html
<input v-model.lazy="msg">
```

- 如果想将用户的输入值自动转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```html
<input v-model.number="age" type="number">
```

上述转换过程是通过 JavaScript 的 `parseFloat()` 函数实现的，在 HTML 中，无论是何种类型的输入元素，其值都是字符串类型。即使在 `<input>` 元素上设置 `type="number"`，其值仍为字符串

* 如果想自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

```html
<input v-model.trim="msg">
```

在 Vue 2 中使用 `v-model` 指令时，Vue 自动尝试将该指令绑定到组件的 `value` prop，并侦听名为 `input` 的事件。但是像单选框、复选框等类型的输入控件可能会将  `value` attribute 用于其他目的。使用 `model` 选项可以避免这样的冲突：

```JavaScript
Vue.component('base-checkbox', {
	model: {
		prop: 'checked',
		event: 'change'
	},
	props: {
		checked: Boolean
	},
	template: `
		<input
			type="checkbox"
			v-bind:checked="checked"
			v-on:change="$emit('change', $event.target.checked)"
		>
	`
})
```

- `prop: 'checked'`：当使用 `v-model` 指令与这个组件进行绑定时，不要查找 `value` prop，而要查找 `checked` prop，即当父组件与子组件使用 `v-model` 进行绑定时，它实际上绑定到 `checked` prop
- `event: 'change'`：这告诉 Vue，不要侦听 `input` 事件来触发更新，而要侦听 `change` 事件。当复选框的选中状态更改时，组件将触发名为 `change` 的事件

* `props: { checked: Boolean }`: 由于 `model` 选项已将 `v-model` 指令映射到名为 `checked` 的 prop，所以父组件可以通过 `v-model` 控制复选框的选中状态
* `v-bind:checked="checked"`: 将 `input` 元素的 `checked` attribute 绑定到 prop 的值，从而反映复选框的选中状态
* `v-on:change="$emit('change', $event.target.checked)"`: 当用户更改复选框的状态时，这将触发一个 `change` 事件，并传递新的选中状态。由于 `model` 选项已将该事件映射到 `v-model`，因此父组件将自动更新其绑定值

###### 为什么将 `<label>` 元素与 `<input>` 元素关联起来?

```html
<label for="username">Username:</label>
<input type="text" id="username">
```

提高无障碍访问和用户体验：

- 视觉和程序上的相关联：标签文本不仅在视觉上与输入元素关联，而且在程序逻辑上也是如此
- 扩大可点击区域：用户可以点击关联的标签来聚焦或激活输入元素，就像直接点击输入元素一样

将  `<label>`  和  `<input>`  元素关联起来，需要给  `<input>`  一个  `id`  属性，给  `<label>`  一个  `for`  属性，其值和  `<input>`  的  `id`  一样

也可以将  `<input>`  直接放在  `<label>`  里，此时不需要  `for`  和  `id`  属性，因为关联已隐含存在：

```html
<label
	> Do you like Clown?
	<input type="checkbox" name="clown">
</label>
```

###### `for` & `form` 与 `label`

- `for` 属性用于将 `<label>` 元素与具有特定 `id` 属性的控件元素（如 `<input>` 元素）关联

* `for` 属性的值应与目标控件元素的 `id` 属性值相匹配。这样，当用户点击该 `<label>` 时，与其关联的控件会获得焦点
* 如果目标元素不是可关联标签（即非表单控件，如段落或标题），`for` 属性将不起作用。此外，如果文档中有多个相同 `id` 的元素，只有第一个与 `for` 属性值匹配的元素会被关联
* `<label>` 元素可以同时具有 `for` 属性和子代控件元素，但 `for` 属性必须指向该子代控件元素

- `form` 属性表示与 `<label>` 元素关联的 `<form>` 元素，即它的表单拥有者
- 该属性的值应为同一文档中 `<form>` 元素的 `id`。这样可以确保 `<label>` 元素与正确的 `<form>` 元素关联，即使它们在 DOM 结构中不是直接的祖先和后代关系
- 通过使用 `form` 属性，可在文档的任何位置放置 `<label>` 元素，而不仅限于作为 `<form>` 元素的后代

```html
<form id="myForm">
	<label for="username" form="myForm">Username:</label>
<</form>
<input type="text" id="username">
```

这个示例中的 `<label>` 元素通过 `for` 属性与 `<input>` 元素关联，并通过 `form` 属性与 `<form>` 元素关联。注意 `<label>` 元素和 `<input>` 元素不需要是相邻的元素或在同一层级
