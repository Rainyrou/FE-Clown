React 核心库 `react` 仅定义组件、Hooks、状态管理和更新逻辑，并不涉及具体平台的渲染，`react-dom`  提供以下能力：

1. 虚拟 DOM 转化为真实 DOM：通过 `ReactDOM.render` 将 `React.createElement` 生成的虚拟 DOM 转换为真实 DOM 并插入到页面中
2. 协调和更新：通过 Diff 算法比较新旧虚拟 DOM 树中节点的差异并根据差异对真实 DOM 进行最小量更新
3. 在浏览器中引入 `react-dom`  以管理 React 组件的生命周期和进行 DOM 操作，支持并发模式及服务端渲染能力

渲染组件：

React17：

```js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

const root = document.getElementById("root");
ReactDOM.render(<App />, root);
```

React18：

```js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

const root = document.getElementById("root");
ReactDOM.createRoot(root).render(<App />);
```

卸载组件：

React17：

```js
ReactDOM.unmountComponentAtNode(root);
```

React18：

```js
root.unmount();
```

服务端渲染：

React17：

```js
import ReactDOM from "react-dom";
const root = document.getElementById("root");
ReactDOM.hydrate(<App />, root);
```

React18：

```js
import ReactDOM from "react-dom/client";
const root = document.createElementById("root");
ReactDOM.hydrateRoot(root, <App />);
```

4. 与其他 React 包的关系：`react` 包定义组件、Hooks、状态管理和更新逻辑，其提供虚拟 DOM 结构，由 `react-dom` 转换为真实 DOM，而 `react-native` 包替代  `react-dom` 以用于移动端开发，渲染移动端原生组件
