[组件基础 — Vue.js](https://v2.cn.vuejs.org/v2/guide/components.html)

###### 文档解读

```JavaScript
	Vue.component('button-counter', {
		data: function() {
			return {
				count: 0
			}
		},
		template: `<button v-on:click="count++">You click me {{ count }} times.</button>`
	})
```

**`data`**: 组件的局部状态。与普通 Vue 实例不同，组件的 `data` 选项必须是一个函数，该函数返回一个对象。这是因为组件可能在应用程序中多次使用，每个实例必须维护自己的独立状态。如果 `data` 是一个对象，则所有实例将共享相同的数据引用，导致一个实例中的更改会影响所有实例

组件是可复用的 Vue 实例，且带有一个名字。我们可以在一个通过 `new Vue` 创建的 Vue 根实例中，把这个组件作为自定义元素来使用：

```html
	<div id="components-demo">
		<button-counter></button-counter>
	</div>
```

```JavaScript
	new Vue ({ el: '#component-demo'})
```

- 组件可被视为扩展的 Vue 实例，它们有很多相同的选项和功能。`new Vue ({ el: '#component-demo'})` 创建一个新的 Vue 根实例，并附加到 id 为 `components-demo` 的元素上，Vue 将在此元素内部进行编译，将自定义组件解析为其定义的结构

- `el`选项用于指定 Vue 实例挂载的元素。它可以是一个 CSS 选择器，也可以是一个实际的 HTML 元素

\*\*底层原理

当 Vue 编译组件时：

- 解析模板: 模板被解析为虚拟 DOM 的结构
- 创建组件实例: 对于每个组件的引用，Vue 都会创建一个新的 Vue 实例，与根实例有着类似的行为
- 数据绑定: 每个组件实例的数据被绑定到它的模板，所以当数据改变时，视图会自动更新

关于 `el` 选项：

- 查询元素: Vue 使用 `document.querySelector` 来查找与 `el` 匹配的元素。如果 `el` 是一个元素，Vue 直接使用它
- 编译模板: 找到元素后，Vue 将编译该元素内部的 HTML 作为模板，即将 HTML 解析为 Vue 可以理解的结构，以便根据数据更新它
- 挂载实例: 编译完成后，Vue 将创建的虚拟 DOM 挂载到实际 DOM 上。从此，Vue 将控制 `el` 元素及其所有子元素

若没有提供 `el` 选项，实例将“未挂载”，且必须显式调用 `vm.$mount()` 来手动挂载它

###### 实例 WTF？

在 Vue 中，一个实例就是 Vue 的构造函数创建的对象。每个 Vue 实例代表一个视图模型，管理模板、数据、方法、计算属性、观察者等。它是 Vue 的核心，连接数据和模板的关键部分

###### 组件的复用

你可以将组件进行任意次数的复用：

```html
	<div id="components-demo">
		<button-counter/>
		<button-counter/>
		<button-counter/>
	</div>
```

注意当点击按钮时，每个组件都会各自独立维护它的  `count`。因为你每用一次组件，就会有它的一个新实例被创建

###### 组件的组织

通常一个应用会以一颗嵌套的组件树的形式来组织，例如，你可能会有页头、侧边栏、内容区等组件，每个组件又包含了其它的像导航链接、博文之类的组件。为了能在模板中使用，这些组件必须先注册以便 Vue 能够识别

###### props

prop 是你可以在组件上注册的一些自定义的 attribute，当一个值传递给一个 prop attribute 时，它就变成了那个组件实例的一个 property

```JavaScript
	Vue.component('blog-post', {
		props: ['title'],
		template: `<h3>{{ title }}</h3>`
	})
```

一个组件默认可以拥有任意数量的 prop，任何值都可以传递给任何 prop。在上述模板中，我们能在组件实例中访问这个值，就像访问  `data`  中的值一样

一个 prop 被注册之后，可以像这样把数据作为一个自定义 attribute 传递进来：

```html
<blog-post title="Hello, Clown !"/>
```

然而在一个典型的应用中，你可能在  `data`  里有一个博文的数组：

```JavaScript
new Vue({
	el: '#blog-post-demo',
	data: {
		posts: [
			{ id: 1, title: 'I am a Clown' },
			{ id: 2, title: 'I am a FE-struggler member' },
			{ id: 3, title: 'I am a xiaoxiao FE' }
		]
	}
})
```

并且想要为每篇文件渲染一个组件，可以使用  `v-bind`  来动态传递 prop

```html
<blog-post
	v-for="post in posts"
	v-bind:key="post.id"
	v-bind:title="post.title"
></blog-post>
```

###### 为什么这个 `data` 不需要 return 语句？

在 Vue 组件中，`data `选项通常是一个函数，并返回一个对象，以确保每个组件实例都有自己的数据副本。但在根 Vue 实例中，`data` 可以直接是一个对象，因为根实例通常只有一个

###### 根实例 & 根元素

根实例是 Vue 应用程序的主入口，可以通过以下特征来识别根实例：

- 根实例通常挂载到一个顶级 HTML 元素上，如 `<div id="app"></div>`
- 根实例可能包含全局可访问的数据和方法，用于在整个应用程序中共享
- 根实例没有父组件，通过检查 `$parent` 属性来验证

```JavaScript
const rootInstance = new Vue({ el: '#app' });
console.log(rootInstance.$parent); // undefined
```

当我们谈到组件的"根元素"时，我们指的是组件模板的最外层 HTML 元素。在 Vue 2.x 中，组件模板必须有一个外层元素，该元素包含模板中的所有其他内容，这个外层元素被称为"根元素"

```html
<h3>{{ title }}</h3>
<div v-html="content"></div>
```

你有两个顶级元素（`<h3>`和`<div>`），这些元素没有共同的父元素将它们包裹起来。这就是为什么 Vue 2.x 会抛出错误的原因，要符合这一限制，可以通过添加共同的父元素来更改代码：

```html
<div>
<h3>{{ title }}</h3>
<div v-html="content"></div>
</div>
```

这个限制主要是由于 Virtual DOM 的实现，组件的根节点是一个虚拟节点，该节点对应于组件模板的唯一根元素。由于根节点是单个对象，因此模板必须只有一个根元素

当组件变得越来越复杂时，为每个相关的信息定义一个 prop 会变得很麻烦：

```html
<blog-post
	v-for="post in posts"
	v-bind:key="post.id"
	v-bind:title="post.title"
	v-bind:content="post.content"
></blog-post>
```

是时候重构一下这个  `<blog-post>`  组件了，让它变成接受一个单独的  `post` prop：

```html
<blog-post
	v-for="post in posts"
	v-bind:key="post.id"
	v-bind:post="post"
></blog-post>
```

```JavaScript
Vue.component('blog-post', {
	props: ['post'],
	template: `
		<div class="blog-post">
			<h3>{{ post.title }}</h3>
			<div v-html="post.content"></div>
		</div>
		`
})
```

- `v-for="post in posts"`: 遍历 `posts` 数组，并为每个 `post` 对象创建一个 `blog-post` 组件实例
- `v-bind:key="post.id"`: 唯一键，用于跟踪每个组件实例的身份
- `v-bind:post="post"`: 将当前循环的 `post` 对象（右侧的 `post`）绑定到组件的 `post` 属性（左侧的 `post`）上，`props: ['post']` 定义了 `post` 属性

* `template` 是一个字符串，包含静态 HTML、Vue 指令、插值语法、动态数据绑定

[API — Vue.js](https://v2.cn.vuejs.org/v2/api/#template)

###### 使用事件抛出一个值

例如我们可能想让  `<blog-post>`  组件决定它的文本要放大多少。这时可以使用  `$emit`  的第二个参数来提供这个值：

```html
<button v-on:click="$emit{'enlarge-text', 0.1}">
	Enlarge text
</button>
```

然后在父级组件监听这个事件的时候，我们可以通过  `$event`  访问到被抛出的这个值：

```html
<blog-post
	...
	v-on:enlarge-text="postFontSize += $event"
></blog-post>
```

未完待续
