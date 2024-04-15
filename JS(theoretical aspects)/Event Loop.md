Event Loop 是 JavaScript 在单线程中为不阻塞主线程而产生的处理异步的核心机制

1. 从宏任务队列中取出一个宏任务，执行

2. 若在执行时产生微任务，则在当前宏任务执行完毕后立即执行它们，直到微任务队列清空

3. 从宏任务队列中取出一个宏任务，执行

4. 重复上述过程，直到所有宏任务微任务被清空

Macrotasks：setTimeout、setInterval、setImmediate、requestAnimationFrame、I/O、UI rendering 等

Microtasks：Promise.then、process.nextTick、MutationObserver
