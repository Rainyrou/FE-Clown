JSON Web Token 作为 JSON 对象，定义自包含方式，包含用户所需信息，避免重复查询数据库，客户端通过 URL、POST或 HTTP header 等方式携带JWT，实现无状态化，服务端无需存储 token，其由三部分构成，以句点 `.` 分隔：

```JSON
const token = base64urlEncoding(header) + '.' + base64urlEncoding(payload) + '.' + base64urlEncoding(signature)
```

- Header 由两部分组成：令牌类型 `type` 及算法 `alg` 如 HMAC SHA256 或 RSA

```JSON
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- Payload 包含实体 (用户) 和其他数据的声明集

```JSON
{
  "loggedInAs": "admin",
  "iat": 1422779638
}
```

- Signature 由 header 和 payload 加密生成，确保其在通信过程中未被篡改

```JSON
HMAC_SHA256(
  secret,
  base64urlEncoding(header) + '.' +
  base64urlEncoding(payload)
)
```

工作流程：当用户登录成功后，服务端返回 JWT 给客户端，客户端于 localStorage 或 Cookie 中储存，当客户端再次请求服务端时携带该 JWT，服务端接收请求后验证该 JWT，若验证成功，则认为该请求合法


