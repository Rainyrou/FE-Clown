Promise 用于解决回调地狱，为异步操作的成功值或失败原因分配事件处理程序，它有 pending、resolved 和 rejected 三种状态，其状态的改变是不可逆的，pending 状态下的 Promise 不会执行回调函数 `then`，必须给 Promise 对象传入一个事件处理程序即回调，否则抛出错误，当一个 Promise 被 resolved 或 rejectd 时，相关的事件处理程序通过事件循环的微任务队列被异步调用，确保 Promise 的事件处理程序是在当前函数执行完后才被调用，尽量避免在 `then` 方法里面定义 rejected 状态的回调函数即 `then` 的第二个参数，而总是使用 `catch` 方法

1. `new Promise((resolve, reject) => {})`：创建一个新 Promise 对象，参数是带有 `resolve` 和 `reject` 两个参数的回调函数，当异步操作成功时调用 `resolve`，失败时调用 `reject`
2. `Promise.prototype.then(onFulfilled, onRejected)`：添加成功 `onFulfilled` 和/或失败 `onRejected` 的回调，它们在 Promise resolved 或 rejectd 时被调用
3. `Promise.prototype.catch(onRejected)`：添加一个 `onRejected` 的回调，其为 `Promise.prototype.then(null, onRejected)` 的语法糖
4. `Promise.prototype.finally(onFinally)`：添加一个在 Promise 不论 resolved 或 rejectd 均执行的回调

静态方法：

1. `Promise.resolve(value)`：返回一个 resolved 给定值的 Promise 对象，若该值是一个 Promise，直接返回这个 Promise
2. `Promise.reject(reason)`：返回一个 rejectd 给定原因的 Promise 对象，同上
3. `Promise.all(iterable)`：接收一个 Promise 可迭代对象作为参数，并返回一个 Promise，当传入的所有 Promise 实例都 resolved 后，返回一个 resolved 的新数组，其包含所有 resolved 结果，若其中一个 Promise 被 rejected，返回的 Promise 直接 rejected，并带有第一个被 rejected 的原因
4. `Promise.allSettled(iterable)`：等待所有给定的 Promise 实例都 resolved 或 rejectd，返回一个新的 Promise 实例，该实例总是 resolved，并带有每个 Promise 的结果对象数组
5. `Promise.race(iterable)`：当传入的 Promise 中有一个首先被 resolved 或 rejectd，则直接返回一个新的 Promise ，其结果由第一个 resolved 或 rejectd 的 Promise 决定，其他Promise 仍会继续执行，但它们的结果不会影响最终结果


判断一个对象是否为 Promise 实例：

```js
const isPromise = (obj) =>
  !!obj &&
  (typeof obj === "object" || typeof obj === "function") &&
  typeof obj.then === "function" &&
  typeof obj.catch === "function";
```