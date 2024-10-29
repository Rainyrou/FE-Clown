[三年不洗澡 - React18 新特性解读](https://juejin.cn/post/7094037148088664078)

新项目直接通过  npm  或  yarn  安装新版依赖即可，老项目将 `package.json` 的依赖改为最新，删除 `node_modules` 并重装即可

React17：渐进式升级

17.0.0 - React17 正式发布
17.0.1 - 只改动单文件，修复 IE 兼容性问题并提升 V8 内部对数组的执行性能 [Comparing v17.0.0...v17.0.1 · facebook/react · GitHub](https://github.com/facebook/react/compare/v17.0.0...v17.0.1)
17.0.2 - 改动集中于 `Scheduler` 包，只与性能统计相关，不涉及主逻辑 [Comparing v17.0.1...v17.0.2 · facebook/react · GitHub](https://github.com/facebook/react/compare/v17.0.1...v17.0.2)

React18  放弃支持 IE11，若需兼容则回退至 React17

React17 通过部分试验性 API 开启 Concurrent Mode，从 React17 同步不可中断更新变为 React18 异步可中断更新，只需将 React17 的 `ReactDOM.render(<App />, root)` 升级为 React18 的 `ReactDOM.createRoot(root).render(<App />)` 即可开启 Concurrent Mode，在 React17 的部分实验性功能里开启 Concurrent Mode 即开启并发更新，但由于 React 官方策略调整，React18 不再依赖 Concurrent Mode 开启并发更新，即开启并发模式并不一定开启并发更新，在 React18 不再有多种模式，而是以使用并发特性作为开启并发更新的依据，而并发更新即交替执行不同任务，当预留的时间不够时，React 将线程控制权交由浏览器，等待下一帧到来，继续执行被中断的任务，时间切片是实现并发更新的具体手段，而并发特性即开启并发模式后具有的特性

- 老架构 -> 不可中断递归方式更新的 Stack Reconciler
- 新架构 -> 可中断遍历方式更新的 Fiber Reconciler

新架构可选择是否开启并发更新，因此目前市面上的 React 版本有以下四种情况：

- React15 及以前：老架构
- React16 和 React17：新架构但未开启并发更新，与前者行为一致

* React18：新架构，未开启并发更新，同时启用并发模式和部分新功能
* React18：新架构，开启并发模式和并发更新

上述均基于 Fiber 实现，其为状态更新提供可中断的能力

* 在旧架构中，Stack Reconciler 采用递归方式执行，无法中断，节点数据保存于递归的调用栈中，而在新架构中，Fiber Reconciler 基于 Fiber 实现，节点数据保存于 Fiber 中
* 各个 Fiber 对应一个组件，保存其类型对应的 DOM 节点信息
* Fiber 节点保存该节点需更新的状态及执行的副作用

1. useTransition

[Patterns for startTransition · reactwg/react-18 · Discussion #100 · GitHub](https://github.com/reactwg/react-18/discussions/100)

```js
import React, { useState, useEffect, useTransition } from "react";

const App: React.FC = () => {
  const [list, setList] = useState<any[]>([]);
  const [isPending, startTransition] = useTransition();
  useEffect(
    () => useTransition(() => setList(new Array(10000).fill(null))),
    []
  );
  return (
    <>
      {list.map((_, i) => (
        <div key={i}>{i}</div>
      ))}
    </>
  );
};

export default App;
```

`useTransition` 将更新任务转换为延迟更新任务，由于 `startTransition` 的回调函数执行 `setList`，因此 `setList` 触发并发更新

2. useDeferredValue

[New in 18: useDeferredValue · reactwg/react-18 · Discussion #129 · GitHub](https://github.com/reactwg/react-18/discussions/129)

`useDeferredValue` 和 `useTransition` 原理相同，其返回一个延迟响应的值，让一个 `state` 延迟生效，只有当前没有紧急更新时，该值才变为最新值

```tsx
import React, { useState, useEffect, useDeferredValue } from "react";

const App: React.FC = () => {
  const [list, setList] = useState<any[]>([]);
  useEffect(() => setList(new Array(10000).fill(null)), []);
  const deferredList = useDeferredValue(list);
  return (
    <>
      {deferredList.map((_, i) => (
        <div key={i}>{i}</div>
      ))}
    </>
  );
};

export default App;
```

3. userId

[Intent to Ship: useId · reactwg/react-18 · Discussion #111 · GitHub](https://github.com/reactwg/react-18/discussions/111)

```js
const id = useId();
```

使同一组件在客户端和服务端生成相同的唯一 ID，解决了 React17 及以前版本中存在的 hydration 不兼容问题，因为 SSR 提供的 HTML 无序，`useId` 中的 `id` 代表该组件在组件树中的层级结构

4. useSyncExternalStore

[Concurrent React for Library Maintainers · reactwg/react-18 · Discussion #70 · GitHub](https://github.com/reactwg/react-18/discussions/70)

由 `useMutableSource`  演变而来，用于解决外部数据撕裂问题，其通过强制同步更新数据让 React 组件在 Concurrent Mode 下安全高效地读取外部数据，React 在 Concurrent Mode 中一次渲染分片执行，中间可能穿插高优先级更新，若在其中改变某些公共数据，则先前的低优先级渲染需重新执行，否则出现渲染结果不一致的问题

`useSyncExternalStore` 在日常业务中无需关注，因为 React 自身的 `useState` 已原生解决了 Concurrent Mode 下的外部数据撕裂问题，而针对框架开发者，一般为第三方状态管理库使用，如 Redux 在状态管理时可能并非直接使用 React 的 `state`，而是在外部维护一个 `store` 对象，通过发布订阅模式实现状态更新，无法依靠 React 自动解决撕裂问题。因此 React 对外提供这一 API

5. useInsertionEffect

[Site Unreachable](https://github.com/reactwg/react-18/discussions/110)

只推荐  `css-in-js` 库使用，用于提前注入  `<style>`  脚本，其原理类似  `useLayoutEffect`，执行时机在 DOM 生成后且在 `useLayoutEffect` 前，但此时无法访问  DOM  节点引用

6. Render API：

`root` 支持并发模式渲染

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

React18  从  `render`  方法中删除回调函数，因为当使用 `Suspense` 时，其无预期效果，若在  `render`  方法中使用回调函数，可在组件中通过  `useEffect`  实现：

React17：

```js
const root = document.getElementById("root");
ReactDOM.render(<App />, root, () => console.log("渲染完成"));
```

React18：

```js
const AppWithCallback = () => {
  useEffect(() => console.log("渲染完成"), []);
  return <App />;
};

const root = document.getElementById("root");
ReactDOM.createRoot(root).render(<AppWithCallback />);
```

若用到 SSR，需将 `hydration` 升级为 `hydrateRoot`：

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

此外 React17 的 FC 默认携带 `children` 属性，而 React18 更新了  TypeScript  类型定义，其 FC 不存在 `children` 属性，定义 `props` 类型时，若需获取子组件 `children`，则需显式定义它

React17：

```ts
interface MyButtonProps {
  color: string;
}

const MyButton: React.FC<MyButtonProps> = ({ children }) => (
  <div>{children} </div>
);

export default MyButton;
```

React18：

```ts
interface MyButtonProps {
  color: string;
  children?: React.ReactNode;
}

const MyButton: React.FC<MyButtonProps> = ({ children }) => (
  <div>{children} </div>
);

export default MyButton;
```

7. setState 自动批处理

React17 只在 React 事件处理函数中自动批处理更新，而在 `promise`、`setTimeout` 和原生事件处理函数中多次更新，React18 在任何情况下均自动批处理，多次更新始终合并为一次

8. flushSync

批处理为破坏性改动，可通过  `flushSync` 退出批量更新，而 `flushSync`  函数内的多个  `setState`  仍为批量更新，如此一来可精准控制哪些部分无需批量更新 [Automatic batching for fewer renders in React 18 · reactwg/react-18 · Discussion #21 · GitHub](https://github.com/reactwg/react-18/discussions/21)

```tsx
import React, { useState } from "react";
import { flushSync } from "react-dom";

const App: React.FC = () => {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  return (
    <div
      onClick={() => {
        flushSync(() => setCount1((count1) => count1 + 1));
        flushSync(() => setCount2((count2) => count2 + 1));
      }}
    >
      <div>count1: {count1}</div>
      <div>count2: {count2}</div>
    </div>
  );
};

export default App;
```

9. Suspense 无需通过 fallback 捕获

[Update to how we handle null or undefined Suspense fallbacks · reactwg/react-18 · Discussion #72 · GitHub](https://github.com/reactwg/react-18/discussions/72)

React17：

若 `Suspense`  组件未提供  `fallback`  属性，React 直接跳过它，继续向上搜索下一边界，但官方发现这可能导致混乱难以调试的情况发生，如正在 Debug 一个问题且在没有 `fallback` 属性的 `Suspense` 组件中抛出一个边界来 check 这一问题，它可能带来意想不到的结果且不会警告说没有 `fallback` 属性

```js
const App = () => {
  return (
    <Suspense fallback={<Loading />}>
      <Suspense>
        <Page />
      </Suspense>
    </Suspense>
  );
};

export default App;
```

React18：

React 以当前组件的 `Suspense`  为边界，即使其 `Suspense`  未提供  `fallback`  属性，我们不再跳过它，而是捕获边界且向外查找，若未找到则 `fallback` 为 `null`。这意味着  `Suspense` 组件仍按预期执行，即便忘记提供 `fallback`  属性

10. React 组件返回值

[Update to allow components to render undefined · reactwg/react-18 · Discussion #75 · GitHub](https://github.com/reactwg/react-18/discussions/75)

在 React17 中，若返回一个空组件，其为 `null`，否则若显式返回  `undefined`，控制台在运行时抛出错误，而在 React18 中，返回一个空组件可为 `null` 或 `undefined`

11. Strict Mode

[Update to React Strict Mode Double Rendering · reactwg/react-18 · Discussion #96 · GitHub](https://github.com/reactwg/react-18/discussions/96)

当使用严格模式时，React 对每个组件进行两次渲染，React17 取消其中一次渲染的控制台日志，而在 React18 中，在 React DevTools 的支持下第二次渲染的控制台日志信息显示为灰色

![[Pasted image 20241029175845.png]]

12. 卸载组件时的更新状态警告

[Update to remove the "setState on unmounted component" warning · reactwg/react-18 · Discussion #82 · GitHub](https://github.com/reactwg/react-18/discussions/82)

![[Pasted image 20241029174627.png]]

此状态警告针对于一些特殊场景如在 `useEffect` 里设置定时器或订阅某事件而在组件内产生副作用但忘记返回一个清理函数清除副作用从而发生内存泄漏。但在实际开发中，更多场景是我们在 `useEffect` 里发送一个异步请求，而异步函数未被 `resolve` 或 `reject` 时就卸载了组件，此种场景同样触发警告，但此时组件内部并没有内存泄漏，因为这个异步函数已被垃圾回收，因此警告是具有误导性的

![222.jpg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/239c3c2b15854e54996a8022bf1cfe31~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

综上 React18 删除这一报错
