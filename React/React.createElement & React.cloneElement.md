创建元素的方式：JSX & `React.createElement`

React 采用 JSX，使得在 JavaScript 代码中可编写类似 HTML 的代码，更接近函数式编程，其提供一种简单直观的方式来声明 UI，实际为一种语法糖，在编译过程中被转换为 JavaScript 对象

```jsx
const element = <h1>Hello, world!</h1>;
```

`React.cloneElement` 用于克隆一个已有的 React 元素并可对其修改

```js
const clonedElement = React.cloneElement(element, props, ...children);
```

- `element`：要复制的 React 元素
- `props`：合并到克隆元素上的新属性
- `children`：可替代原有子元素的新子元素