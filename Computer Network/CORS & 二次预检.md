出于安全原因，浏览器通过同源策略禁止一个源的 HTML 或 JavaScript 获取另一源资源以防止 XSS 攻击，但其限制合法跨域请求如 API 调用，而 CORS 为 HTTP 头部驱动的机制，由大多数现代浏览器实现，用于服务端声明哪些源即哪些域、协议或端口可与之交互

1. 浏览器向服务端发送请求，首先检查该请求是否为简单请求如 GET、HEAD 或 POST 请求且请求头部符合 W3C 标准定义的 CORS 安全请求头部集合，若为简单请求，则直接发送该请求且在请求头中添加 `Origin` 请求头部字段，否则，浏览器先发送 OPTIONS 请求（预检），询问服务端对上述请求是否允许
2. 服务端响应：服务端检查其来自哪个源并检查是否位于跨域白名单，若是，服务端在响应中添加 `Access-Control-Allow-Origin` 头部字段，其值即为允许的源

- 请求头：Origin + Access-Control-Request-Methods + Access-Control-Request-Methods
- 响应头：Access-Control-Allow-Origin + Access-Control-Allow-Methods + Access-Control-Allow-Headers + Access-Control-Allow-Credentials

二次预检：请求方法 & 请求头部字段变化 + 刷新页面 & 请求 URL 变化 + CORS 预检缓存过期
