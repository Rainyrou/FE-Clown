###### 请求头

- `Content-Type` 报文类型
- `Content-Length` 内容长度
- `Accept` 允许接收的数据类型
* `Accept-Encoding` 允许接收的压缩方式
- `Cookie` 用户凭证，包含服务器通过 `Set-Cookie` 响应头发送的一个或多个 Cookie
- `Connection` 完成传输是否关闭 TCP 连接，`keep-alive`（保持连接）和 `close`（关闭连接）
- `Host` 访问的主机域名，指定请求的目标服务器
- `User-Agent` 浏览器类型，标识发起请求的客户端类型
- `Cache-Control` 缓存策略，如 `no-cache`、`max-age` 等
- `Expires` 缓存策略，已被上面的替代

###### 响应头

* `Content-Type` 报文类型
* `Content-Length` 内容长度
* `Content-Encoding` 内容编码，指定响应体使用的压缩方式
* `Set-Cookie` 设置 Cookie
* `Access-Control-Allow-Origin` CORS
* `Connection` 完成传输是否关闭 TCP 连接，`keep-alive`（保持连接）和 `close`（关闭连接）
* `Server` 服务器信息，如 `Apache`、`Nginx`
* `Location` 重定向目标地址
- `Cache-Control` 缓存策略，如 `no-cache`、`max-age` 等
- `Expires` 缓存策略，已被上面的替代
- `Etag` 协商缓存限制条件，提供资源的唯一标识符（通常是一个哈希值），用于确定资源在服务器上是否已被修改
- `Last-Modified` 协商缓存限制条件，标识资源最后一次被修改的时间
