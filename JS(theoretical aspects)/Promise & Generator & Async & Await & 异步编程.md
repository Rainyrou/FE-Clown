Promise 用于解决回调地狱，为异步操作的成功或失败分配事件处理程序，其有 pending、resolved 和 rejected 状态，状态变化不可逆即只能从 pending 转换为 resolved/rejected，而无法反过来

1. `new Promise`：创建新的 Promise 对象，参数为带有 `resolve` 和 `reject` 参数的回调，当异步操作成功时调用 `resolve`，失败时调用 `reject`
2. `Promise.prototype.then(onFulfilled, onRejected)`：添加 `onFulfilled` 和 `onRejected` 的回调，在 Promise resolved 或 rejectd 时调用 `onFulfilled` 或 `onRejected`
3. `Promise.prototype.catch(onRejected)`：添加 `onRejected` 的回调，在 Promise rejectd 时调用 `onRejected`
4. `Promise.prototype.finally(onFinally)`：添加 `onFinally` 的回调，无论 Promise resolved 或 rejectd 均调用

静态方法：

`Promise.resolve/Promise.reject`：返回一个 resolved/rejected 的 Promise 对象

`Promise.all` + `Promise.allSettled` + `Promise.race`，参数为 Promise 可迭代对象，返回一个新的 Promise。`Promise.all` - > 当所有 Promise 实例 resolved，结果为由所有 Promise 实例 resolved 组成的 resolved 结果对象数组，否则为首个 Promise 实例被 rejected 的原因的 rejected 结果对象数组；`Promise.allSettled` - > 结果为由所有 Promise 实例 resolved/rejected 组成的 resolved 结果对象数组；`Promise.race` - > 结果由首个 resolved/rejected Promise 实例决定

判断对象是否为 Promise 实例：

```js
const isPromise = (obj) =>
  !!obj &&
  (typeof obj === "object" || typeof obj === "function") &&
  typeof obj.then === "function" &&
  typeof obj.catch === "function";
```

Generator 对象遵循 ES6 迭代器协议，通过在函数名前添加星号定义，内部通过 `yield` 暂停函数执行 ，通过 `next` 恢复函数执行，调用 Generator 返回指针对象

Async/Await 为 Generator 和 Promise 的语法糖，解决回调地狱、Generator 手动控制 `next` 和 Promise 链式调用等问题，以同步形式代码实现异步编程

异步编程即设法让其类似同步编程，在等待耗时任务完成时，不阻塞主线程，继续处理其他任务，待耗时任务完成后，再回头执行其后续逻辑

ES6 前的异步编程方法：回调地狱 + 事件监听器 + `setTimeout` & `setInterval` 定时器 + 发布订阅 & 观察者模式 + 流（Node.js）