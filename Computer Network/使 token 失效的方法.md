token，尤其是用于身份验证的 token（如 JWT），并不内置机制来识别其是否失效。它们通常有一个过期时间（如 `exp` 声明在 JWT 中），而验证它们的服务器会检查这个时间以决定它们是否已过期，但这并不会真正使 token 失效，这只是一种检查 token 是否应该被认为是失效的的方式

为了真正使 token 失效，必须有某种机制来记住哪些 token 是无效的，需要有一个存储（如数据库或缓存）记录已发出但现在被认为是失效的 token

1. 过期时间：设置 token 的生存期非常短，这样即使 token 被窃取，攻击者也只有很短的时间来使用它
2. 黑名单：当 token 被认为是不安全的，可以将它添加到黑名单中。每次接收到请求时，检查 token 是否在黑名单中。如果在就拒绝该请求
3. 数据库存储：与 token 相关的状态可以存储在数据库中。如当用户退出或更改密码时，可以使当前的 token 失效
4. 双 token 机制：使用 access token 和 refresh token。access token 有很短的生存期，而 refresh token 有更长的生存期。当 access token 过期时，使用 refresh token 来获取新的 access token。当 refresh token 被认为是不安全的，可以使其失效，从而使与之相关的所有 access token 也失效
5. 绑定 token 到特定的 IP 或设备：这样即使 token 被窃取，攻击者也无法从其他设备或 IP 上使用它，那 token 自然就失效了
