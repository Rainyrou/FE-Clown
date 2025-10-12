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

JWT：JSON 对象，其包含用户信息，客户端通过 URL、POST 或 HTTP header 等方式携带 JWT 以实现无状态化，服务端无需存储 token，避免重复查询数据库，其由 Header + Payload + Signature 构成，以 `.` 分隔：

- Header 由令牌类型 `type` 和加密算法构成
- Payload 包含用户实体和其他数据声明集
- Signature 由 Header 和 Payload 加密生成

Access Token：用于访问受保护的资源如 API，客户端在有效期内可用其认证状态和访问权限，其具有较短的有效期，风险相对较低，即便被泄露，攻击者也只能在短时间内使用它
Refresh Token：用于在 Access Token 过期后获取一个新的 Access Token，无需用户再次登录，Refresh Token 有更长的有效期，其安全性比 Access Token 高，因为它只用于与认证服务端通信，而不是直接用来访问受保护的资源

1. 用户首次登录时，基于 OAuth 2.0 或 OpenID Connect 的认证服务端在认证用户后，同时发送 Access Token 和 Refresh Token 给客户端
2. 客户端使用 Access Token 访问受保护资源，若有效，则请求成功，若无效或过期，则使用 Refresh Token 来获取一个新的 Access Token，认证服务端验证 Refresh Token 的有效性，若验证成功，发送新的 Access Token
3. 客户端使用新的 Access Token 访问受保护资源，直到 Access Token 再次过期，若 Refresh Token 也过期，用户需重新登录

Token 失效：双 token 机制 + 过期时间 + 黑名单 + 与特定 IP&设备绑定
