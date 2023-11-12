Event Loop 是 JavaScript 在单线程环境中为了不阻塞主线程而产生的处理异步任务的核心机制。它在 JavaScript 运行时环境检查执行栈是否为空。如果执行栈为空，它会从消息队列中取出一个消息并执行它。如果执行栈不为空，它会等待直到栈为空

执行栈是一个后进先出（LIFO）的数据结构，用于跟踪函数执行的过程。当一个函数被调用，它被添加到栈顶。当这个函数执行完毕，它会从栈顶弹出，返回控制权给调用它的函数

在 JavaScript 中任务分为两大类：

Macrotasks：setTimeout、setInterval、setImmediate、requestAnimationFrame、I/O、UI rendering 等

Microtasks：Promise 的 then 方法、process.nextTick、MutationObserver

1. 执行整体的 script 作为一个宏任务

2. 执行当前宏任务中的所有代码

3. 如果在执行当前宏任务时产生了微任务，在当前宏任务结束后立即执行这些微任务，直到微任务队列清空

4. 如果有任何待处理的 UI 渲染任务，此时处理

5. 执行下一个宏任务

6. 重复步骤 2-5，直到所有任务都被处理
