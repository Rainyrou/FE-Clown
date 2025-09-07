浏览器：用户交互事件 -> 一个宏任务 -> 所有微任务 -> requestAnimationFrame -> DOM 渲染 + CSS 计算 + 布局+ 绘制 -> requestIdleCallback

宏任务队列：`script` 代码块、`setTimeout` 和 `setInterval`
微任务队列：`Promise` 回调、`async` `await`、`process.nextTick`、`MutationObserver`
渲染任务队列：DOM 渲染、CSS 计算、布局（Reflow）、绘制（Repaint）
延迟任务队列：`requestIdleCallback` 注册的任务如后台数据同步和日志记录

Node.js 的事件循环与浏览器的是完全不同的概念，V8 引擎解析 JavaScript，而 libuv 库调用 Node.js API，将不同任务分配给不同线程而形成事件循环，以异步方式将执行结果返回给 V8 引擎

Node.js：同步任务 -> 所有微任务 -> 一个宏任务

MutationObserver 用于监听 DOM 变化，浏览器 DOM 节点通过父子关系以树形结构形式存储，浏览器记录任何对DOM树的增删改操作并存储于内部的 Mutation Record 队列。在浏览器的事件循环中，在执行完当前宏任务后，`MutationObserver` 的回调函数作为微任务执行，其遍历内部的 Mutation Record 队列，处理记录的 DOM 变化，此种按需触发机制比持续遍历完整 DOM 树的轮询机制有更佳的性能，但其无法直接监听样式变化和事件的增删操作

| 配置选项            | 作用                              |
| --------------- | ------------------------------- |
| childList       | 监听子节点的增删                        |
| attributes      | 监听属性值变化                         |
| subtree         | 是否监听节点及其子节点                     |
| characterData   | 监听节点及其子节点文本/内容变化                |
| attributeFilter | 指定监听的某些属性如 `['class', 'style']` |

`MutationObserver` 回调的 `mutationsList` 为一个 `MutationRecord` 对象数组，各个对象均记录一次 DOM 变化，其主要属性包括：

| 属性          | 说明                                                                       |
| ------------- | -------------------------------------------------------------------------- |
| type          | 变化的类型：`attributes`、`childList` 和 `characterData`                   |
| target        | 变化的节点                                                                 |
| addedNodes    | 增加的子节点                                                               |
| removedNodes  | 删除的子节点                                                               |
| attributeName | 修改的属性名                                                               |
| oldValue      | 变化前的值，当 `attributeOldValue` 或 `characterDataOldValue` 为 `true` 时 |

```js
const targetNode = document.getElementById("target");

const config = {
  childList: true,
  attributes: true,
  subtree: true,
  characterData: true,
};

const observer = new MutationObserver((mutationList, observer) => {
  for (const mutation of mutationList) {
    if (mutation.type === "childList") console.log("子节点发生变化", mutation);
    else if (mutation.type === "attributes")
      console.log("属性发生变化", mutation.attributeName);
  }
});

observer.observe(targetNode, config);
observer.disconnect();
```
