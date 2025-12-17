SSO：

- 首次登录：用户首次访问目标资源，服务提供商判断用户无本地会话，根据信任规则生成认证请求，通过 302 临时重定向至认证页面
- 身份验证：身份提供商判断用户是否存在全局会话，若不存在跳转至登录页面，否则直接复用全局会话，根据信任规则生成凭证（包含用户和服务提供商的唯一标识、凭证有效期和凭证签名）
- 凭证回传：身份提供商通过 302 临时重定向至服务提供商的回调地址，服务提供商接收到凭证后，发送请求给身份提供商，身份提供商接收请求后，通过公钥解密凭证签名，验证服务提供商的唯一标识和凭证有效期并返回结果
- 创建会话：服务提供商接收到结果后，创建本地会话并将用户重定向至目标资源
- 免登录认证：用户访问信任体系下其他服务提供商，该服务提供商判断用户无本地会话后，通过 302 临时重定向至认证页面，身份提供商判断用户存在全局会话，生成该服务提供商的认证凭证，回传给服务提供商并完成身份验证，服务提供商创建本地会话
- 单点登出：用户在任意服务提供商或身份提供商发送登出请求，身份提供商销毁全局会话，通过回调通知所有关联的服务提供商销毁本地会话

JWT：JSON 对象，其包含用户信息，客户端通过 URL、POST 或 HTTP header 等方式携带 JWT 以实现无状态化，服务端无需存储和重复查询数据库，其由 Header + Payload + Signature 构成，以 `.` 分隔：

- Header 由令牌类型和加密算法构成
- Payload 包含用户实体和其他数据声明集
- Signature 由 Header 和 Payload 加密生成

Access Token：用户访问目标资源，有效期较短
Refresh Token：在 Access Token 失效后获取新 Access Token，有效期较长

- 用户首次登录，服务端认证成功后发送 Access Token 和 Refresh Token 给客户端
- 客户端通过 Access Token 访问目标资源，若 Access Token 失效则通过 Refresh Token 获取新 Access Token，再通过 Access Token 访问同一资源，若 Refresh Token 失效则用户需重新登录以获取新 Access Token 和 Refresh Token

Token 失效：双 token 机制 + 过期时间 + 黑名单 + 与特定 IP & 设备绑定

服务端将 Cookie 发送给客户端，客户端存储 Cookie 且同源请求自动携带 Cookie，服务端据此判断用户身份，客户端会话对应唯一 SESSION ID，服务端将用户数据存储于 SESSION ID 对应的文件中，客户端请求携带 SESSION ID，服务端据此判断用户身份；业界通用方案为将 SESSION ID 存储于 Cookie

不同点：

- Cookie 将数据存储于客户端，Session 将数据存储于服务端
- Cookie 存储 String 类型，Session 存储 Object 类型
- 在客户端中篡改 Cookie 的难度较小，在服务端中篡改 Session 的难度较大
- 同源请求自动携带 Cookie，服务端多域共享Session
- Cookie 大小由客户端控制，不超过 4KB，Session 大小由服务端控制

Cookie 属性：

- Name 和 Value：Cookie 由 Name 和 Value 的键值对组成，以 `=` 分隔
- Expires/Max-Age：指定 Cookie 的生命时间，默认为会话级别
- Domain：指定接收 Cookie 的域，默认为创建 Cookie 的域
- Path：指定 Cookie 的页面路径，默认为创建 Cookie 的页面路径
- Secure：指定 Cookie 只能通过 HTTPS 发送
- HttpOnly：JavaScript 及其他脚本无法访问 Cookie，防止 XSS 攻击
- SameSite：防止 CSRF 攻击

服务端跨域设置：

- `Set-Cookie`
- `Access-Control-Allow-Credentials/Access-Control-Allow-Origin`
- `Access-Control-Allow-Headers/Access-Control-Allow-Methods`（预检请求）
- `Secure/HttpOnly/SameSite=None`

客户端跨域设置：`withCredentials/Origin`

删除 Cookie：通过 `document.cookie` 覆盖机制匹配 Cookie 作用域 Domain 和页面路径 Path，设置 Expires -> 过期时间 + Max-Age -> 0
