[三年不洗澡 - React18 新特性解读](https://juejin.cn/post/7094037148088664078)

新项目通过 pnpm/npm/yarn  安装最新依赖即可，老项目手动修改 `package.json` 依赖为最新，删除重装过 `node_modules` 即可

React17：渐进式升级

17.0.0 - React17 正式发布
17.0.1 - 改动单文件，修复 IE 兼容性问题并提升 V8 内部对数组的执行性能 [Comparing v17.0.0...v17.0.1 · facebook/react · GitHub](https://github.com/facebook/react/compare/v17.0.0...v17.0.1)
17.0.2 - 改动集中于 `Scheduler` 包，与性能统计相关，不涉及主逻辑 [Comparing v17.0.1...v17.0.2 · facebook/react · GitHub](https://github.com/facebook/react/compare/v17.0.1...v17.0.2)

React18 放弃支持 IE11，若需兼容则回退至 React17

React17 通过部分试验性 API 开启 Concurrent Mode，从 React17 同步不可中断更新变为 React18 异步可中断更新，只需将 React17 的 `ReactDOM.render(<App />, root)` 升级为 React18 的 `ReactDOM.createRoot(root).render(<App />)` 即可开启 Concurrent Mode，在 React17 的部分实验性功能中开启 Concurrent Mode 即开启并发更新，但由于 React 官方调整，React18 不再依赖 Concurrent Mode 开启并发更新即开启并发模式与并发更新无直接关系，以并发特性为开启并发更新的依据，并发更新即交替执行不同任务，当浏览器预留的时间不足时，React 将线程控制权传递给浏览器，等待浏览器下一帧到来，继续执行被中断的任务

- 老架构 -> 不可中断递归方式更新的 Stack Reconciler
- 新架构 -> 可中断遍历方式更新的 Fiber Reconciler

1. useTransition + useDeferredValue + useId + useSyncExternalStore + useInsertionEffect
2. Render API：

并发渲染：

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

SSR：

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

3. setState 自动批处理 & flushSync：setState 自动批处理在 React17 只适用于 React 合成事件，在 React18 适用于 React 合成事件、原生事件、Promise 和定时器等任何场景，通过 `flushSync` 退出自动批处理 [Automatic batching for fewer renders in React 18 · reactwg/react-18 · Discussion #21 · GitHub](https://github.com/reactwg/react-18/discussions/21)

4. Suspense 无需通过 fallback 捕获：在 React17 中若 `Suspense`  组件不提供  `fallback` 属性，React 直接跳过，继续向上搜索下一边界，但可能导致意外行为，在 React18 中，React 以当前组件的 `Suspense`  为边界，若其不提供  `fallback` 属性，React 捕获边界并继续向上搜索下一边界，若未找到则 `fallback` 为 `null`，`Suspense` 组件仍按预期执行

[Update to how we handle null or undefined Suspense fallbacks · reactwg/react-18 · Discussion #72 · GitHub](https://github.com/reactwg/react-18/discussions/72)

5. React 组件返回值：在 React17 中，若返回空组件则其为 `null`，若显式返回 `undefined`，运行时抛出错误，在 React18 中，返回空组件可为 `null` 或 `undefined`

[Update to allow components to render undefined · reactwg/react-18 · Discussion #75 · GitHub](https://github.com/reactwg/react-18/discussions/75)

6. Strict Mode：严格模式下 React 为各个组件进行两次渲染，React17 移除其中一次渲染的控制台日志， React18 的第二次渲染对应控制台日志信息为灰色

[Update to React Strict Mode Double Rendering · reactwg/react-18 · Discussion #96 · GitHub](https://github.com/reactwg/react-18/discussions/96)

![[Pasted image 20241029175845.png]]

7. 删除卸载组件时的更新状态警告：React17 设计该警告以针对在 `useEffect` 产生副作用但未通过清理函数清除副作用导致内存泄漏的场景，但在 `useEffect` 发送异步请求，其未被 resolved 或 rejected 即卸载组件，同样触发警告，但组件不存在内存泄漏，因此有误导性的警告被 React18 删除

[Update to remove the "setState on unmounted component" warning · reactwg/react-18 · Discussion #82 · GitHub](https://github.com/reactwg/react-18/discussions/82)

![[Pasted image 20241029174627.png]]

![222.jpg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/239c3c2b15854e54996a8022bf1cfe31~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)
