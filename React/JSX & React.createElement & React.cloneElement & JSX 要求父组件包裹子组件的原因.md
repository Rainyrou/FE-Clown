创建元素的方式：JSX & `React.createElement`

JSX 为 React 中类似 XML 的语法扩展，使得在 JavaScript 代码中可编写类似 HTML 的代码，更接近函数式编程，其提供简单直观的方式以声明 UI（语法糖），通过 Babel 转换为 JavaScript，通过 `React.createElement` 生成虚拟 DOM，通过 `ReactDOM.render` 将虚拟 DOM 转换为真实 DOM 并插入到页面中，其中 Fiber 架构调度和管理虚拟 DOM 更新，Diff 算法比较新旧虚拟 DOM 树中节点的差异并根据差异对真实 DOM 进行最小量更新

```jsx
const element = <h1>Hello, world!</h1>;
```

`React.cloneElement` 用于克隆已有的 React 元素并对其修改

```js
const clonedElement = React.cloneElement(element, props, ...children);
```

- `element`：React 源元素
- `props`：合并至 React 目标元素的新属性
- `children`：替代原先子元素的新子元素

JSX 要求父组件包裹子组件的原因：JSX 为 React 中类似 XML 的语法扩展，通过 Babel 转换为 JavaScript，通过 `React.createElement` 生成虚拟 DOM，`React.createElement(element, props, ...children)` 接收单根节点为核心入参，后续参数为子节点列表，若 JSX 存在多个同级子组件且无父组件包裹，编译后生成多个并列的 `React.createElement` 调用，而 JavaScript 语法不允许"return 语句后直接返回多个无父容器包裹的表达式"
