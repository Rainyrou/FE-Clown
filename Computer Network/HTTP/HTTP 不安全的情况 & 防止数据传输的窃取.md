1. 中间人攻击：若攻击者在客户端与服务端间插入自己，如在公共 WiFi 上提供伪造的 DNS 服务，攻击者可将用户重定向到恶意网站
2. 弱加密算法 & 过时的 SSL/TLS 版本
3. 证书问题：服务端证书不是由受信任的证书颁发机构 CA 签发或证书已过期
4. 页面静态资源未经 HTTPS 加载
5. 软件漏洞

###### 安全性

1. HTTPS
2. HSTS：HTTP Strict Transport Security 使客户端只能通过 HTTPS 与服务端通信，在响应头中指定`Strict-Transport-Security` 字段实现
3. TLS 配置：禁用弱加密算法，使用安全的密码套件，启用 TLS 1.2 或更高版本
4. 双向 SSL/TLS 认证：除服务端认证，客户端也应提供证书，实现双向 SSL/TLS 认证
5. CT：Certificate Transparency 是一个公开的日志系统，用于记录和检测 SSL/TLS 证书，防止证书误发或恶意发放，使用支持 CT 的浏览器和服务，确保访问网站的证书已在 CT 日志中注册
6. DNSSEC：DNS Security Extensions 为 DNS 响应提供数字签名，确保 DNS 记录的真实性和完整性，防止 DNS 缓存污染攻击。在 DNS 服务器上启用 DNSSEC，并确保客户端支持 DNSSEC 验证
7. 加密数据
8. 访问控制和最小权限原则：只允许特定用户和系统访问敏感数据，实施基于角色的访问控制 RBAC，为不同用户和服务分配最小的权限集合
9. 定期更新
10. 监控和日志记录
