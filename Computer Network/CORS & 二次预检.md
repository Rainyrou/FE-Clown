出于安全原因，Web 浏览器采用同源策略（Same-Origin Policy, SOP），禁止一个源的 HTML 或 JS 访问获取另一个源的资源，这可以防止 XSS 攻击。但它也限制了合法的跨域请求，例如 API 调用。CORS 是一个 HTTP 头部驱动的机制，由大多数现代浏览器实现，它允许服务器声明哪些源（即哪些域、协议或端口）是被允许与其进行交互的，用于控制哪些 Web 页面可以请求跨域的资源

###### 工作流程

1. 简单请求：当你使用 ajax & axios & fetch 从一个源获取另一个源的资源时，浏览器会首先检查该请求是否为"简单请求"（如 GET、HEAD 或 POST 请求，且不设置某些特定的 HTTP 头）。如果为简单请求，浏览器直接发出该请求，并在请求头中加入一个 `Origin` 字段

2. 预检请求：若不满足上述条件，浏览器会首先发送一个 HTTP OPTIONS 请求，即"预检"请求。这个预检请求询问目标服务器对上述请求是否允许

3. 服务器响应：服务器检查来自哪个源的请求，并检查这个源是否在其跨域白名单中。如果是，服务器会在其 HTTP 响应中加入一个 `Access-Control-Allow-Origin` 头，其值即为允许的源（ `*` 表示允许任何源）

   对于预检请求，服务器还会加入其他头，如 `Access-Control-Allow-Methods` 和 `Access-Control-Allow-Headers`

###### 响应头 & 请求头

在 CORS（跨域资源共享）的上下文中，涉及到的一些重要的 HTTP 请求头和响应头如下：

- 请求头：

1. Origin：浏览器在发起跨域请求时自动添加，用于标明该请求来自哪个源。例如：`Origin: https://example-client.com`
2. Access-Control-Request-Methods：仅在预检请求中出现，用于告知服务器真实请求将使用的 HTTP 请求方法
3. Access-Control-Request-Headers：仅在预检请求中出现，用于告知服务器真实请求将使用的 HTTP 头部字段

- 响应头：

1. Access-Control-Allow-Origin：服务器对 CORS 请求的回应中必须包含此头部，它的值要么是请求 `Origin` 头部的确切值，要么是一个 `*`，表示接受任何域的请求
2. Access-Control-Allow-Methods: 仅在预检请求中出现，表示服务器允许的 HTTP 请求方法
3. Access-Control-Allow-Headers: 仅在预检请求中出现，表示服务器允许的 HTTP 头部字段
4. Access-Control-Allow-Credentials: 一个布尔值，表示是否允许发送 cookies。如果其值为 `true`，表示允许发送 cookies，但此时 `Access-Control-Allow-Origin` 不能为 `*`
5. Access-Control-Expose-Headers: 表示哪些响应头可以被浏览器的 XMLHttpRequest 对象处理
6. Access-Control-Max-Age: 表示预检请求的返回结果可被缓存的时间

###### 二次预检

当出现以下变化时，重新触发二次预检的情况：

1. 请求的 HTTP 方法改变：例如，如果你先发送了一个使用 `POST` 方法的 CORS 请求，然后发送了一个使用 `PUT` 方法的 CORS 请求，浏览器可能会为 `PUT` 请求重新发送预检请求
2. 请求头发生变化：如果你在 CORS 请求中增删了自定义的请求头，或者修改了其值，浏览器可能会重新发送预检请求
3. 重新访问或刷新页面：预检请求的结果可能在某些浏览器中会被缓存一段时间。但如果用户重新访问或刷新了页面，浏览器可能会重新发送预检请求
4. 请求 URL 发生变化：如果请求的 URL 发生了微小变化（即使只是查询参数的变化），浏览器可能会重新发送预检请求
5. 浏览器的 CORS 预检缓存到期：浏览器会根据 `Access-Control-Max-Age` 响应头的值来缓存预检请求的结果。当缓存到期后，浏览器会重新发送预检请求

总之，任何影响 CORS 策略决策的变化都可能导致浏览器重新发送预检请求。这是为了确保浏览器在处理跨域请求时总是遵循最新和最安全的策略
