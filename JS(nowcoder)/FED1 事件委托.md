请补全JavaScript代码，要求如下：  
1. 给"ul"标签添加点击事件  
2. 当点击某"li"标签时，该标签内容拼接"."符号。如：某"li"标签被点击时，该标签内容为".."  

注意：  
1. 必须使用DOM0级标准事件（onclick）


![[1692510668436.png]]

- `document.querySelector('ul')`：这是一个 DOM 查询函数，返回文档中匹配给定的 CSS 选择器的第一个元素。在这里，它返回文档中的第一个 `ul` 元素

- `event.target`：这是事件对象的一个属性，它返回触发该事件的元素。例如，如果你在一个按钮上点击，`event.target` 会是那个按钮

- `target.tagName`：每个 DOM 元素都有一个 `tagName` 属性，该属性返回元素的名称（例如："DIV", "LI", "A" 等）。注意，`tagName` 返回的名称通常都是大写的

- `.toLowerCase()`：这是一个字符串方法，用于将字符串转换为小写

- `target.innerText`：这是一个 DOM 元素的属性，它返回或设置元素的文本内容

[Document - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Document)

[document.querySelector() - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelector)

[Event.target - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/target)

##### `document.querySelector` 官方文档解读

`querySelector()` 方法是属于 DOM 的 `Document` 的一个方法。它用于在整个文档中查找与给定 CSS 选择器匹配的第一个元素。如果没有找到与该选择器匹配的元素，该方法将返回 `null`。在查找匹配项（DFS）时，从文档的第一个元素开始，然后进入其子元素，然后其子元素的子元素，依此类推，直到找到一个匹配的元素为止

```JavaScript
element = document.querySelector(selectors);
```

- `element` 是返回的元素（如果有的话）
- `selectors` 是你要查询的 CSS 选择器

* 简单地说，`selectors` 参数是一个或多个 CSS 选择器的字符串。如果这个字符串不是有效的 CSS 选择器，就会抛出一个 `SYNTAX_ERR` 异常
* 在 CSS 和 JavaScript 中，某些字符有特殊的含义，因此当你想在选择器中使用它们时，你需要使用反斜杠 (`\`) 来转义它们
* 如果你想获取与选择器匹配的所有元素，而不仅仅是第一个，你应该使用 `querySelectorAll()` 方法

##### `Event.target` 官方文档解读

- `Event.target` 是 `Event` 接口的一个只读属性
- 当事件处理程序在冒泡或捕获阶段被调用时，`Event.target` 与 `event.currentTarget` 是不同的，`event.currentTarget` 是引用添加事件监听器的元素，而 `Event.target` 是实际触发事件的元素

##### 事件介绍 官方文档解读

[事件介绍 - 学习 Web 开发 | MDN](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Building_blocks/Events)

- 事件可被认为是系统中的特定行为或发生的事情，通常是由用户触发的。例如，当用户点击一个按钮或移动鼠标，事件就会被触发。事件不仅仅是用户触发的，它还可以由系统或应用程序本身触发
- 事件通常关联于特定的 DOM 元素，但也可能关联于整个文档或浏览器窗口。例如，点击事件可以附加到特定的按钮，而窗口调整大小的事件则关联于整个浏览器窗口
- 事件处理器（事件监听器），是你为处理特定事件编写的代码或函数。例如，当按钮被点击时，你可能想执行一段代码，那么你可以为那个按钮的点击事件添加一个事件处理器
- 当你为某个事件关联一个处理器或监听器时，这个过程被称为"注册"
- 事件和事件处理并不是 JavaScript 语言内置的，而是由浏览器提供的 API 定义的

###### 示例：处理点击事件

```html
<button>改变颜色</button>
```

```JavaScript
const btn = document.querySelector('button');

const random = (number) => {
	return Math.floor(Math.random() * (number + 1));
}

btn.addEventListener('click', () => {
	const rndColor = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
	document.body.style.backgroundColor = rndColor;
});
```

- `Math.random()` 生成一个介于 0（包括）和 1（不包括）之间的随机小数。`Math.floor()` 函数将该小数向下取整

* `addEventListener()` 方法可为一个 HTML 元素添加事件监听器。每一个 DOM 元素都有一个 `addEventListener()` 方法，它接受两个参数：

1. 事件类型：一个表示事件名称的字符串

不是所有事件都适用于所有 DOM 元素。例如，`play` 事件主要用于 `<audio>` 和 `<video>` 元素

2. 事件处理函数：当指定的事件发生时，要执行的函数

###### 监听其他事件

- `focus` 和 `blur`：当元素获得或失去焦点时，会触发这两个事件
- `dblclick`：当在元素上双击时，会触发此事件
- `mouseover` 和 `mouseout`：当鼠标指针悬停在元素上或离开元素时，会触发这两个事件

###### 移除监听器

- 如果不再需要某个事件监听器，可以使用 `removeEventListener()` 将它移除

```JavaScript
btn.removeEventListener('click', changeBackground);
```

删除之前添加到 `btn` 上的` changeBackground` 事件处理函数

- 使用 `AbortController` 和 `AbortSignal` 来移除事件处理器

```JavaScript
const controller = new AbortController();

btn.addEventListener('click', () => {
	const rndColor = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
	document.body.style.backgroundColor = rndColor;
}, { signal: controller.signal })
```

然后你可以像这样删除上面代码创建的事件处理器：

```JavaScript
controller.abort();
```

###### 在单个事件上添加多个监听器

通过对  `addEventListener()`  的多次调用，可以为单个事件设置多个处理器：

```JavaScript
myElement.addEventListener("click", functionA);
myElement.addEventListener("click", functionB);
```

当点击按钮时，所有处理器函数都会执行

###### 事件处理器属性

几乎所有 DOM 元素都有一系列的事件处理器属性，每一个属性都对应着一个可能的事件。例如，所有的 DOM 元素都有一个 `onclick` 属性，这个属性用于响应点击事件。这些属性的名称以 `on` 为前缀，后面跟着事件的名称。为了监听事件，你可将一个函数分配给这些属性，这个函数就是事件发生时要执行的处理函数

```JavaScript
btn.onclick = () => {
  const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
  document.body.style.backgroundColor = rndCol;
};
```

- 事件处理器属性不能为单个事件设置多个处理器

###### 事件对象

当事件发生时，浏览器会创建一个事件对象，其中包含了与该事件相关的所有信息。当你设置了一个事件监听器并指定了一个回调函数，这个事件对象会自动被传递给该回调函数作为它的第一个参数。这个事件对象通常被命名为 `event`、`evt` 或 `e`，但你可以随意命名

```JavaScript
function bgChange(e) {
  const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
  e.target.style.backgroundColor = rndCol;
  console.log(e);
}
```

`e` 是事件对象。`e.target` 表示触发事件的元素

###### 阻止默认行为

阻止默认行为是指在某个事件被触发时，阻止浏览器执行与该事件相关的默认行为。这通常通过调用事件对象的 `preventDefault()` 方法来实现

```html
<form>
	<div>
		<label for="fname">First name: </label>
		<input id="fname" type="text" />
	</div>
	<div>
		<label for="lname">Last name: </label>
		<input id="lname" type="text" />
	</div>
	<div>
		<input id="submit" type="submit" />
	</div>
</form>
<p />
```

```JavaScript
const form = document.querySelector('form');
const fname = document.getElementById('fname');
const lname = document.getElementById('lname');
const para = document.querySelector('p');

form.addEventListener('submit', (e) => {
	if(fname.value === '' || lname.value === '') {
		e.preventDefault();
		para.textContent = 'You need to fill in both names!'
	}
})
```

###### 事件冒泡

事件冒泡用于描述事件如何从最具体的元素（即触发该事件的元素）开始，逐级向上逐层传递，直到被处理或到达文档的最顶级元素

当你在某个元素上触发一个事件，不仅该元素会触发这个事件，它的所有父元素也会依次触发这个事件。这种事件传播机制分为：

1. 捕获阶段：事件从最外层的父元素向内传播，直到目标元素
2. 冒泡阶段：事件从目标元素向外传播，直到最外层的父元素

默认情况下，事件处理程序是在冒泡阶段进行处理的在上述示例中，当按钮被点击时，点击事件首先在按钮上触发，然后冒泡到其父元素`<div>`，再冒泡到更外层的元素，依此类推，直到 `<html>` 元素或事件被阻止

```html
<div id="container">
	<button>点我捏！</div>
</div>
<pre id="output" />
```

```JavaScript
const output = document.querySelector('#output');
const handleClick = (e) => {
	output.textContent += `你在 ${e.currentTarget.tagName} 元素上进行了点击\n`
}

const container = document.querySelector('#container');
container.addEventListener('click', handleClick);
```

监听器被添加到 ID 为"container"的 `<div>` 元素上。当该 `<div>` 元素或其子元素被点击时，都会触发这个监听器

```html
<body>
	<div id="container">
		<button>点我捏！</div>
	</div>
	<pre id="output" />
</body>
```

```JavaScript
const output = document.querySelector('#output');
const handleClick = (e) => {
	output.textContent += `你在 ${e.currentTarget.tagName} 元素上进行了点击\n`
}

const container = document.querySelector('#container');
const button = document.querySelector('button');

document.body.addEventListener('click', handleClick);
container.addEventListener('click', handleClick);
button.addEventListener('click', handleClick);
```

毫无疑问，输出显示为：

```
你在 BUTTON 元素上进行了点击
你在 DIV 元素上进行了点击
你在 BODY 元素上进行了点击
```

再来一个示例：

```html
<button>显示视频</button>

<div class="hidden">
	<video>
		<source src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.webm" type="video/webm"/>
		<p 你的浏览器不支持 HTML 视频，这里有视频的<a href="rabbit320.mp4">替代链接</a>。 />
	</video>
</div>
```

```JavaScript
const btn = document.querySelector('button');
const box = docuemnt.querySelector('div');
const video = document.querySelector('video');

btn.addEventListener('click', () => box.classList.remove('hidden'));
video.addEventListener('click', () => video.play());
box.addEventListener('click', () => box.classList.add('hidden'));
```

我们希望的是：

- 当用户单击“显示视频”按钮时，显示包含视频的盒子，但视频没有播发
- 当用户在视频上单击时，开始播放视频
- 当用户单击盒子内视频以外的任何区域时，隐藏盒子

当你点击视频来播放它时，容器也随之被隐藏了。这是因为当你点击视频时，不仅触发了视频的点击事件处理器，还由于事件冒泡的特性，触发了其父容器 `div` 的点击事件处理器

为避免这种情况，我们可以在 `video` 的事件处理器中调用 `Event.stopPropagation()` 方法。这会阻止事件冒泡，确保点击视频时不会触发容器 `div` 的点击事件处理器

```JavaScript
video.addEventListener('click', (e) => {
	video.play();
	e.stopPropagation();
});
```

现在，当你点击视频时，它只会播放，而不会影响其父容器

###### 事件捕获

在 `addEventListener` 中，你可以通过设置第三个参数 `{ capture: true }` 来使事件处理程序在捕获阶段运行。默认情况下，如果不指定或指定为 `false`，则事件处理程序将在冒泡阶段运行

```html
<body>
	<div id="container">
		<button>点我捏！</div>
	</div>
	<pre id="output" />
</body>
```

```JavaScript
const output = document.querySelector('#output');
const handleClick = (e) => {
	output.textContent += `你在 ${e.currentTarget.tagName} 元素上进行了点击\n`
}

const container = document.querySelector('#container');
const button = document.querySelector('button');

document.body.addEventListener('click', handleClick, { capture: true });
container.addEventListener('click', handleClick, {capture: true });
button.addEventListener('click', handleClick);
```

此时的输出为：

```
你在 BODY 元素上进行了点击
你在 DIV 元素上进行了点击
你在 BUTTON 元素上进行了点击
```

当你点击 `button` 时，首先触发 `body` 的事件处理器（捕获阶段），接着是 `div` 的事件处理器（仍在捕获阶段），最后是 `button` 的事件处理器（冒泡阶段）

###### 事件委托

事件委托是一利用事件冒泡机制来优化事件处理程序的技术。如果一个元素的多个子元素都需要监听相同的事件，那么只需要在它们共同的父元素上设置一个事件监听器，而无需在每一个子元素上单独设置

```html
<div id="container">
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
  <div class="tile"></div>
</div>
```

```css
.tile {
  height: 100px;
  width: 25%;
  float: left;
}
```

```JavaScript
const random = (number) => {
  return Math.floor(Math.random() * number);
}

const bgChange = () => {
  const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
  return rndCol;
}

const container = document.querySelector('#container');

container.addEventListener('click', (event) => {
  event.target.style.backgroundColor = bgChange();
});
```

如果后来向容器中动态添加了新的子元素，由于事件监听器是添加到父元素上的，新添加的子元素自动受益于这个监听器，无需再次为新元素添加事件监听器