出于安全原因，浏览器采用同源策略，禁止一个源的 HTML 或 JS 获取另一个源的资源以防止 XSS 攻击，但它也限制合法的跨域请求如 API 调用。CORS 是一个 HTTP 头部驱动的机制，由大多数现代浏览器实现，用于服务端声明哪些源即哪些域、协议或端口是被允许与其进行交互的

###### 工作流程

1. 简单请求：当你使用 AJAX & Axios & Fetch 一个源的 HTML 或 JS 获取另一个源的资源时，浏览器首先检查该请求是否为简单请求如 GET、HEAD 或 POST 请求，且不设置某些特定 HTTP 请求头部字段，若为简单请求，浏览器直接发送该请求且在请求头中添加一个 `Origin` 字段
2. 预检请求：若不满足上述条件，浏览器首先发送一个 OPTIONS 请求即预检请求，其询问服务端对上述请求是否允许
3. 服务端响应：服务端检查来自哪个源的请求，并检查其是否在跨域白名单中，若是，服务端在其响应头中添加一个 `Access-Control-Allow-Origin` 字段，其值即为允许的源

###### 响应头 & 请求头

- 请求头：

1. Origin：浏览器在发送跨域请求时自动添加，用于标明该请求来自哪个源。例如：`Origin: https://example-client.com`
2. Access-Control-Request-Methods：仅在预检请求中出现，用于告知服务端真实请求使用的请求方法
3. Access-Control-Request-Headers：仅在预检请求中出现，用于告知服务端真实请求使用的头部字段

- 响应头：

1. Access-Control-Allow-Origin：服务端对 CORS 请求之响应需包含的字段，其值为请求 `Origin` 头部值或 `*`
2. Access-Control-Allow-Methods: 仅在预检请求中出现，表示服务端允许的请求方法
3. Access-Control-Allow-Headers: 仅在预检请求中出现，表示服务端允许的头部字段
4. Access-Control-Allow-Credentials: 表示是否允许发送 Cookies，其值为 `true` 表示允许发送，`Access-Control-Allow-Origin` 不能为 `*`
5. Access-Control-Expose-Headers: 表示哪些响应头可被浏览器的 XMLHttpRequest 对象处理
6. Access-Control-Max-Age: 表示预检请求的返回结果可缓存的期限

###### 二次预检

1. 请求方法 & 请求头部改变
2. 重新访问 & 刷新页面 & 请求 URL 改变
3. 浏览器的 CORS 预检缓存到期
