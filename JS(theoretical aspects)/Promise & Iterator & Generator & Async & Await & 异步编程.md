Promise 解决回调地狱，为异步操作的成功或失败分配事件处理程序，有 pending、resolved 和 rejected 状态，通过构造函数的执行器函数 `resolve` 和 `reject` 实现状态变化，状态变化不可逆即从 pending 转换为 resolved/rejected 而无法反向

1. `new Promise`：创建新 Promise 对象，参数为带有 `resolve` 和 `reject` 参数的回调，当异步操作成功时调用 `resolve`，失败时调用 `reject`
2. `.then(onFulfilled, onRejected)`：参数为 `onFulfilled` 和 `onRejected` 回调，在 Promise resolved 或 rejectd 时调用 `onFulfilled` 或 `onRejected`；若传参为非函数则等同于 `.then(null)`，此时传递前一 Promise 结果；`.then`  返回值若为 Promise 本身则导致死循环
3. `.catch(onRejected)`：参数为 `onRejected` 回调，在 Promise rejectd 时调用 `onRejected`；`.catch`  返回值若为 Promise 本身则导致死循环
4. `.finally(onFinally)`：参数为 `onFinally` 回调，无论 Promise resolved 或 rejectd 均调用，回调本身不接收任何参数，其返回值为前一 Promise 结果，但若抛出异常则返回异常 Promise 对象

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

Iterator：

- 遵循 ES6 迭代器协议，调用 `Symbol.iterator` 返回 Iterator 本身
- 调用 `next` 返回由当前元素 value 和是否结束 done 构成的对象
- done 为 false -> value 为当前元素 + done 为 true -> value 为遍历结束值
- 内部通过数组索引和对象属性指针维护遍历状态

Generator：

* 遵循 ES6 迭代器协议，调用 `Symbol.iterator` 返回 Generator 本身，通过在函数名前添加星号定义
* 内部调用 `yield` 暂停函数执行 ，调用 `next` 恢复函数执行，调用 Generator 函数返回指针对象

Async/Await 为 Generator 和 Promise 的语法糖，解决回调地狱、Generator  手动控制 `next` 和 Promise 链式调用等问题，以同步形式代码实现异步编程，`await`  本质为暂停当前 `async` 函数执行并将  `await`  对应行代码后的逻辑（即下一行及之后的代码）包装为新微任务，当 `await` 等待的 Promise 为 rejected 时，其中断当前 `async` 函数执行并将其返回的 Promise 也为 rejected

异步编程即设法让其类似同步编程，在等待耗时任务完成时，不阻塞主线程，继续处理其他任务，待耗时任务完成后，再回过头来执行其后续逻辑

ES6 前的异步编程方法：回调地狱 + 事件监听器 + `setTimeout` & `setInterval` 定时器 + 发布订阅 & 观察者模式 + 流（Node.js）
