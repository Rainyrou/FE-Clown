JWT (JSON Web Token) 是一种互联网开放标准，定义了一种自包含的方式，作为 JSON 对象用于在客户端和服务端之间传递信息，它通常由三部分组成，用句点 (`.`) 分隔：

```JSON
const token = base64urlEncoding(header) + '.' + base64urlEncoding(payload) + '.' + base64urlEncoding(signature)
```

- Header 通常由两部分组成：令牌类型 (`type`，通常为 "JWT") 和所使用的算法 (`alg`，如 HMAC SHA256 或 RSA)

```JSON
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- Payload 包含一个实体 (通常是用户) 和其他数据的声明集

```JSON
{
  "loggedInAs": "admin",
  "iat": 1422779638
}
```

- Signature 由 header 和 payload 加密生成，确保它们在传输过程中没有被篡改

```JSON
HMAC_SHA256(
  secret,
  base64urlEncoding(header) + '.' +
  base64urlEncoding(payload)
)
```

###### 工作流程

- 当用户登录成功后，服务端会返回一个 JWT 给客户端
- 客户端会储存这个 JWT，通常是在 localStorage 或者 cookie 中
- 当客户端再次请求服务端时，会在请求头中带上这个 JWT
- 服务端接收到请求后会验证这个 JWT，如果验证成功，服务端会认为这个请求是合法的

###### 为什么使用 JWT ？

- 实现无状态化。服务端不需要存储 token，每次客户端发送请求，只需要带上该 JWT
- JWT 轻量、结构简单，可以通过 URL、POST 参数或在 HTTP header 里发送 
- JWT 的信息集是自包含的，其中包含所有用户需要的信息，避免多次查询数据库

###### 局限性

虽然 JWT 提供了一种简单的认证方法，但也存在安全隐患。如果 JWT 被拦截，它可以被任何有该 JWT 的人使用。因此需要使用 HTTPS 来传输


