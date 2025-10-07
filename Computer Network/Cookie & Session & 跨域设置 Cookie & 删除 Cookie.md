服务端将 Cookie 发送于客户端，客户端存储 Cookie 且各个同源请求自动携带 Cookie，服务端据此判断用户身份，而客户端会话对应唯一 SESSION ID，服务端将用户数据存储于 SESSION ID 对应的文件或内存中，客户端请求携带 SESSION ID，服务端据此判断用户身份；现代方案为将 SESSION ID 存储于 Cookie

不同点：

- Cookie 将数据存储于客户端，Session 将数据存储于服务端
- Cookie 存储 String 类型，Session 存储 Object 类型
- 用户在客户端中修改伪造 Cookie，在服务端中无法直接修改伪造 Session
- 由于浏览器同源策略限制，Cookie 在同源情况下发送，而 Session 在服务端多域共享
- Cookie 大小由客户端控制，不超过 4KB，而 Session 大小由服务端灵活控制

Cookie 属性：

1. Name 和 Value：Cookie 由 Name 和 Value 的键值对组成，以 `=` 分隔
2. Expires/Max-Age：指定 Cookie 的生命时间，默认为会话级别
3. Domain：指定接收 Cookie 的域，默认为创建 Cookie 的域
4. Path：指定 Cookie 的有效路径，默认为创建 Cookie 的页面路径
5. Secure：指定 Cookie 只能通过 HTTPS 发送
6. HttpOnly：JavaScript 及其他脚本无法访问 Cookie，防止 XSS 攻击
7. SameSite：防止 CSRF 攻击

服务端跨域设置：

1. `Set-Cookie`
2. `Access-Control-Allow-Credentials/Access-Control-Allow-Origin`
3. `Access-Control-Allow-Headers/Access-Control-Allow-Methods`（预检请求）
4. `Secure/HttpOnly/SameSite=None`

客户端跨域设置：`withCredentials/Origin`

删除 Cookie：通过 `document.cookie` 覆盖机制，精确匹配 Cookie 作用域 Domain 和 Path，Expires -> 过期时间 + Max-Age -> 0
