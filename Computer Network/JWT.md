JSON Web Token 定义一种自包含的方式，作为 JSON 对象用于在客户端和服务端间传递信息，它由三部分组成，用句点 `.` 分隔：

```JSON
const token = base64urlEncoding(header) + '.' + base64urlEncoding(payload) + '.' + base64urlEncoding(signature)
```

- Header 由两部分组成：令牌类型 `type` 和所使用的算法 `alg` 如 HMAC SHA256 或 RSA

```JSON
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- Payload 包含一个实体 (通常为用户) 和其他数据的声明集

```JSON
{
  "loggedInAs": "admin",
  "iat": 1422779638
}
```

- Signature 由 header 和 payload 加密生成，确保它们在传输过程中未被篡改

```JSON
HMAC_SHA256(
  secret,
  base64urlEncoding(header) + '.' +
  base64urlEncoding(payload)
)
```

###### 工作流程

- 当用户登录成功后，服务端返回一个 JWT 给客户端
- 客户端在 localStorage 或 cookie 中储存该 JWT
- 当客户端再次请求服务端时，在请求头中携带该 JWT
- 服务端接收到请求后验证该 JWT，若验证成功，服务端认为该请求是合法的

###### 为什么使用 JWT ？

- 实现无状态化，服务端无需存储 token，每次客户端发送请求，只需携带该 JWT
- JWT 轻量、结构简单，可通过 URL、POST 参数或在 HTTP header 里发送
- JWT 的信息集是自包含的，其中包含所有用户需要的信息，避免多次查询数据库

###### 局限性

虽然 JWT 提供一种简单的认证方法，但存在安全隐患，若其被拦截，它可被任何拥有该 JWT 的人使用，因此需用 HTTPS 进行传输
