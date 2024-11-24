MutationObserver 用于监听 DOM 变化，浏览器的 DOM 以树形结构的形式存储，各个节点均可视为一个对象，这些对象通过父子关系构成树，`MutationObserver` 基于微任务队列和浏览器内部对 DOM 树的修改记录，每次操作 DOM 树如增删改，浏览器记录这些操作于一个内部的 Mutation Record 队列，在浏览器的事件循环中，执行完当前宏任务后处理所有微任务，而 `MutationObserver` 的回调函数作为微任务被执行，遍历 Mutation Record 队列，处理记录的 DOM 变化。相比于传统的轮询机制如以 `setInterval` 定期检查 DOM，`MutationObserver` 有更好的性能，因为其由浏览器内部 DOM 操作直接触发而非持续遍历整个 DOM 树。但其无法直接监听样式变化如 `style`（通过监听 `attributes` 并以 `mutation.attributeName` 监听 `style` 属性解决），无法监听事件的增删（与 DOM 结构无关），监听大量节点时的性能开销过大（避免无意义的全局监听，按需监听）

| 配置选项        | 作用                                      |
| --------------- | ----------------------------------------- |
| childList       | 监听子节点的增删                          |
| attributes      | 监听属性值变化                            |
| subtree         | 是否监听节点及其子节点                    |
| characterData   | 监听节点及其子节点文本/内容变化           |
| attributeFilter | 指定监听的某些属性如 `['class', 'style']` |

`MutationObserver` 回调的 `mutationsList` 为一个 `MutationRecord` 对象数组，每个对象记录一次 DOM 变化，其主要属性包括：

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
