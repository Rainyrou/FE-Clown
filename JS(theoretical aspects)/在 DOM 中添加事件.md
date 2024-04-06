1. 在 HTML 中直接添加事件处理器属性如 `onclick`，将其值设置为 JavaScript 代码

```html
<button onclick="alert('Hello World')">click me</button>
```

2. `addEventListener` 为 DOM 元素添加事件监听器

```js
document.getElementById('myButton').addEventListener('click', () => alert('Hello, world!'));
```
