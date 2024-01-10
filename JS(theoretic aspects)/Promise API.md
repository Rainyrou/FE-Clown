Promise 是 ES6 引入的，它旨在解决回调地狱的问题，提供了一种更加强大且灵活的方式处理异步操作

1. `new Promise((resolve, reject) => {})`

- 创建一个新的 Promise 对象。参数是带有 `resolve` 和 `reject` 两个参数的回调函数。当异步操作成功时调用 `resolve`，失败时调用 `reject`

2. `Promise.prototype.then(onFulfilled, onRejected)`

- 添加成功 `onFulfilled` 和/或失败 `onRejected` 的回调处理程序。这些回调会在 Promise resolved 或 rejectd 时被调用

3. `Promise.prototype.catch(onRejected)`

- 添加一个 `onRejected` 的回调处理程序，它是 `Promise.prototype.then(null, onRejected)` 的语法糖

4. `Promise.prototype.finally(onFinally)`

- 添加一个在 Promise 不论 resolved 或 rejectd 都会执行的回调函数

###### 静态方法

1. `Promise.resolve(value)`

- 返回一个 resolved 了给定值的 Promise 对象。如果这个值是一个 Promise，将返回这个 Promise

2. `Promise.reject(reason)`

- 返回一个 rejectd 了给定原因的 Promise 对象

3. `Promise.all(iterable)`

- 当传入的所有 Promise 实例都 resolved 后，返回一个 resolved 的新 Promise 实例。如果其中任何一个 Promise 被 rejected，返回的 Promise 就直接 rejected

4. `Promise.allSettled(iterable)`

- 等待所有给定的 Promise 实例都 resolved 或 rejectd，返回一个新的 Promise 实例，该实例总是 resolved，并带有每个 Promise 的结果对象数组

5. `Promise.race(iterable)`

- 当传入的 Promise 实例中有一个首先 resolved 或 rejectd，返回一个新的 Promise 实例，其状态和结果由首先 resolved 或 rejectd 的 Promise 决定

###### 底层原理

Promise 对象代表了一个可能在未来才会知道结果的异步操作，它允许你为异步操作的成功值或失败原因分配处理程序。Promise 有三种状态：pending、resolved 和 rejected。Promise 状态的改变是不可逆的

当一个 Promise 被 resolved 或 rejectd 时，相关的处理程序（then/catch/finally）会被异步调用（通过事件循环的微任务队列），这确保了 Promise 的处理程序总是在当前脚本执行完毕之后才被调用
