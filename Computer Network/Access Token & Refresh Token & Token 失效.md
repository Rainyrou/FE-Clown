Access Token：用于访问受保护的资源如 API，客户端在有效期内可用其认证状态和访问权限，其具有较短的有效期，风险相对较低，即便被泄露，攻击者也只能在短时间内使用它
Refresh Token：用于在 Access Token 过期后获取一个新的 Access Token，无需用户再次登录，Refresh Token 有更长的有效期，其安全性比 Access Token 高，因为它只用于与认证服务端通信，而不是直接用来访问受保护的资源

1. 用户首次登录时，基于 OAuth 2.0 或 OpenID Connect 的认证服务端在认证用户后，同时发送 Access Token 和 Refresh Token 给客户端
2. 客户端使用 Access Token 访问受保护资源，若有效，则请求成功，若无效或过期，则使用 Refresh Token 来获取一个新的 Access Token，认证服务端验证 Refresh Token 的有效性，若验证成功，发送新的 Access Token
3. 客户端使用新的 Access Token 访问受保护资源，直到 Access Token 再次过期，若 Refresh Token 也过期，用户需重新登录

Token 失效：双 token 机制 + 过期时间 + 黑名单 + 与特定 IP&设备绑定
