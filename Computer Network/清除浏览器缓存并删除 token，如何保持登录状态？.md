###### 1. Access Token & Refresh Token

1. 用户首次登录时，基于 OAuth 2.0 或 OpenID Connect 的认证服务端在认证用户后，同时发送 Access Token 和 Refresh Token 给客户端
2. 客户端使用 Access Token 访问受保护资源，若有效，则请求成功，若无效或过期，则使用 Refresh Token 来获取一个新的 Access Token，认证服务端验证 Refresh Token 的有效性，若验证成功，发送新的 Access Token
3. 客户端使用新的 Access Token 访问受保护资源，直到 Access Token 再次过期，若 Refresh Token 也过期，用户需重新登录

###### 2. 服务端会话

服务端跟踪用户会话，非完全依赖于客户端 token。用户登录后，服务端创建会话并存储在数据库或内存中，它返回会话标识如会话 ID，客户端存储于 Cookie 中。用户在后续请求中携带会话标识，服务端通过会话标识查找对应会话。若用户清除浏览器缓存，丢失本地存储的会话标识，但可通过某种机制如自动登录 Cookie来恢复会话

###### 3. 自动登录

用户登录时选择自动登录选项，服务端生成一个长期有效的自动登录 token，其存储在 Cookie 中，当用户访问且无有效的会话时，服务端使用该 token 自动创建新的会话


