- 100 Continue：客户端继续发送请求的其余部分
- 200 OK：请求成功
- 201 Created：请求成功，服务端创建新资源
- 204 No Content：请求成功，只返回响应头而没有返回响应体
- 206 Partial Content：请求成功，只返回部分资源
- 301 Moved Permanently：永久重定向，在响应头部的 `Location` 字段指定新 URL
- 302 Found：临时重定向，在响应头部的 `Location` 字段指定临时 URL
- 303 See Other：重定向至其他资源
- 304 Not Modified：资源未修改，用于协商缓存
- 307 Temporary Redirect：类似 302，但请求方法无法修改
- 308 Permanent Redirect：类似 301，但请求方法无法修改
- 400 Bad Request：客户端请求报文格式有误，服务端无法解析该请求
- 401 Unauthorized：请求需用户身份认证
- 403 Forbidden：服务端拒绝执行请求，禁止访问该资源
- 404 Not Found：服务端未找到请求对应的资源
- 405 Method Not Allowed：请求方法不被允许
- 500 Internal Server Error：服务端内部发生未知错误
- 501 Not Implemented：服务端暂不支持请求所需功能
- 502 Bad Gateway：服务端作为网关或代理时，从上游服务端接收到无效响应
- 503 Service Unavailable：服务端暂时无法处理请求
- 504 Gateway Timeout：网关超时，从上游服务端未接收到响应

请求头：

- `Content-Type` 报文类型
- `Content-Length` 内容长度
- `Accept` 允许接收的数据类型
* `Accept-Encoding` 允许接收的压缩方式
- `Cookie` 用户凭证，包含服务器通过 `Set-Cookie` 响应头部字段发送的一或多个 Cookie
- `Connection` 完成传输后是否关闭 TCP 连接，`keep-alive`（保持连接）和 `close`（关闭连接）
- `Host` 访问的主机域名，指定请求的目标服务器
- `User-Agent` 浏览器类型，标识发起请求的客户端类型
- `Cache-Control` 缓存策略如 `no-cache`、`max-age` 等
- `Expires` 缓存策略，已被上面的替代

响应头：

* `Content-Type` 报文类型
* `Content-Length` 内容长度
* `Content-Encoding` 内容编码，指定响应体使用的压缩方式
* `Set-Cookie` 设置 Cookie
* `Access-Control-Allow-Origin` CORS
* `Connection` 完成传输后是否关闭 TCP 连接，`keep-alive`（保持连接）和 `close`（关闭连接）
* `Server` 服务器信息，如 `Apache`、`Nginx`
* `Location` 重定向目标地址
- `Cache-Control` 缓存策略，如 `no-cache`、`max-age` 等
- `Expires` 缓存策略，已被上面的替代
- `Etag` 协商缓存限制条件，提供资源的唯一标识符（通常为一哈希值），用于确定资源在服务器上是否已被修改
- `Last-Modified` 协商缓存限制条件，标识资源最后一次被修改的时间
