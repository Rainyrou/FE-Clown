###### ajax

Asynchronous JavaScript and XML 是一种使用现有标准的方法，其核心是 XMLHttpRequest 对象，用于在后台与服务器通信，获取数据

- 可以异步更新网页的一部分，而无需重新加载整个页面
- 使用 JavaScript 进行动态交互

###### axios

axios 是一个基于 Promise 的 HTTP 客户端，用于浏览器和 Nodejs 中。其拦截器基于 AOP（面向切面编程）的理念。它允许你在函数执行的前后进行预处理或自定义操作，而不必修改函数本身，这使得代码易于维护和复用

- 拦截请求和响应
- 取消请求
- 转换请求数据和响应数据
- 自动转换 JSON 数据
- 支持 Promise API
- 从浏览器中创建 XMLHttpRequests
* 从 Node.js 创建 HTTP 请求
- 客户端支持防止 CSRF

###### fetch

Fetch API 提供了一个 JavaScript 接口，用于访问和操纵 HTTP 管道的部分，例如请求和响应。它还提供了一个全局 `fetch()` 方法，该方法提供了一种简单、合理的方式来跨网络异步获取资源

- 基于 Promise 设计
- 替代 XMLHttpRequest 和更传统的方法如 jQuery.ajax
- 无需依赖额外的库或框架
- 原生支持 JSON
- 不支持取消请求和原生的进度监控