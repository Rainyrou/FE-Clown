###### ajax

Asynchronous JavaScript and XML 是一种使用现有标准的方法，其核心是 XMLHttpRequest 对象，用于在后台与服务端通信，获取数据

- 可异步更新网页的一部分，而无需重新加载完整页面
- 使用 JavaScript 进行动态交互

###### axios

axios 是一个基于 Promise 的 HTTP 客户端，用于浏览器和 Nodejs 中。其拦截器基于 AOP（面向切面编程）的理念。它允许你在函数执行的前后进行预处理或自定义操作，而不必修改函数本身，这使得代码易于维护和复用

- 拦截请求和响应
- 取消请求
- 转换请求数据和响应数据
- 自动转换 JSON 数据
- 支持 Promise API

###### fetch

Fetch API 提供了一个 JavaScript 接口

- 不支持取消请求和原生的进度监控
- 替代 XMLHttpRequest 和更传统的方法如 jQuery.ajax
- 无需依赖额外的库或框架
- 原生支持 JSON
- 基于 Promise 设计
