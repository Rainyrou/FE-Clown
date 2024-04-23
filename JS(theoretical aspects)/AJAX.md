[Ajax 知识体系大梳理 - 掘金](https://juejin.cn/post/6844903469896171533?searchId=202404230806386C14AAC7589AA688075C#heading-56)

Asynchronous JavaScript and XML，标准浏览器通过 `XMLHttpRequest`  对象实现 AJAX 的功能，IE 浏览器通过  `XMLHttpRequest`  或 `ActiveXObject`  对象同样实现 AJAX 的功能，XMLHttpRequest 实例对象没有自有属性，其所有属性来自于 `XMLHttpRequest.prototype`，`XMLHttpRequest` -> `XMLHttpRequest.prototype` -> `XMLHttpRequestEventTarget.prototype` -> `EventTarget.prototype` -> `Object.prototype`

浏览器线程机制：GUI 渲染线程 + JS 引擎线程 + 浏览器事件触发线程 + HTTP 请求线程

线程间交互以事件的形式触发, 通过事件回调予以通知，而事件回调, 又是以先进先出的方式添加到任务队列的末尾 , 直到 JS 引擎空闲时, 任务队列中的任务依次执行，即事件循环。对于一个 AJAX 请求, JS 引擎首先生成 `XMLHttpRequest` 实例对象, `open` 后调用 `send` 方法，它们都是同步执行的，但从 `send` 方法内部开始, 浏览器为即将发生的网络请求创建新的 HTTP 请求线程，其独立于 JS 引擎线程, 于是网络请求被异步发送，而 JS 引擎不会等待 AJAX 发送的 HTTP 请求接收到响应，它依照顺序继续执行任务队列中的任务。当 AJAX 请求被服务端响应且接收到 `response` 后, 浏览器事件触发线程捕获 AJAX 的事件回调 `onreadystatechange`，该事件回调并没有立即执行, 而是被添加到任务队列的末尾.，直到 JS 引擎空闲时，任务队列中的任务依次执行。`onreadystatechange` 事件内部操作 DOM，浏览器挂起 JS 引擎线程，执行 GUI 渲染线程，进行重绘回流，而后浏览器挂起 GUI 渲染线程，执行 JS 引擎线程，UI 渲染被加入到任务队列。JS 引擎线程和 GUI 渲染线程互斥，其他线程并行执行，AJAX 没有破坏 JS 的单线程机制

![[Pasted image 20240423083413.png]]
