1. 从宏任务队列中取出一个宏任务，执行
2. 若在执行过程中产生微任务，则在当前宏任务执行完毕后立即执行直到微任务队列清空
3. 从宏任务队列中取出一个宏任务，执行
4. 重复上述过程，直到所有宏任务微任务执行完毕

Macrotasks：`setTimeout`、`setInterval`、`setImmediate`、`I/O`、`UI rendering` 和 `requestAnimationFrame` 等
Microtasks：`Promise.then`、`process.nextTick` 和 `MutationObserver`
