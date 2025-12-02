JavaScript 设计之初是为浏览器提供轻量级页面交互功能，当时的 Web 页面无需复杂业务逻辑，单线程足以应对绝大多数业务场景，在多线程环境中，通过同步机制和锁等并发访问共享资源，但引入死锁和性能损耗等新问题，因此 JavaScript 在设计之初通过单线程形式简化前端开发，无需考虑多线程并发和数据一致性等问题。JavaScript 的执行环境通过事件循环机制支持异步编程且现代 JavaScript 通过 Web Worker 和 Service Worker 等方式模拟多线程处理，Web Worker、Service Worker 为浏览器中独立于主线程运行的线程，有其独立的上下文、全局对象、事件循环和生命周期，Web Worker 执行计算密集或长任务，Service Worker 实现离线支持、后台同步和推送通知等

Node.js 线程：

- JavaScript 主线程
- 通过 libuv 库线程池处理阻塞 I/O 操作和 CPU 密集型计算，当事件完成后置于事件队列，JavaScript 主线程通过事件循环机制从事件队列取出已完成的事件并在主线程执行其回调
- 通过  `worker_threads`  创建独立 JavaScript 线程
