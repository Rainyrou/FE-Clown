创建元素的方式：JSX & `React.createElement`

1. JSX：React 采用 JSX，使得在 JavaScript 代码中可以写类似 HTML 的代码，更接近于函数式编程。虽然使用 JSX 不是强制的，但它广泛用于 React 生态系统中，提供一种简单直观的方式来声明 UI 结构。JSX 实际上是一种语法糖，它在编译过程中被转换为普通的 JavaScript 对象，React 提供 `React.createElement` 方法来创建这些对象，编译器把 JSX 转换为相应的 `React.createElement` 调用

```jsx
const element = <h1>Hello, world!</h1>;
```

2. `React.createElement` 方法接收三个参数：字符串、属性对象及任意数量的子元素

```js
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```
