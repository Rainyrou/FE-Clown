数字签名：

- 发送方计算生成原始消息的哈希值，通过私钥加密该哈希值，生成数字签名，发送原始消息和数字签名至接收方
- 接收方通过公钥解密数字签名，获取哈希值 1，通过计算生成原始消息的哈希值 2
- 若哈希值 1 等于哈希值 2，则验证成功

CA 证书签发：

- CSR 构建：服务端生成非对称密钥，将公钥、域名和身份信息封装为 CSR 并通过私钥加密生成数字签名
- 身份验证：CA 根据级别验证身份，DV 验证域名控制权，OV 添加组织身份，EV 添加法律资质
- CSR 验证：通过公钥解密数字签名，获取哈希值 1，通过计算生成原始消息的哈希值 2，若哈希值 1 等于哈希值 2，则验证成功
- 证书生成：绑定身份信息 + 公钥并通过 CA 私钥签名生成数字证书
- 证书链构建：证书通过中间 CA 签发，服务端部署证书与私钥，SSL/TLS 握手时服务端将数字证书发送给客户端，客户端通过用户证书 → 中间 CA 证书 → 根 CA 证书逐级验证

SSL/TLS 握手：

- 客户端发送 ClientHello 给服务端，其包含客户端支持的 TLS 版本，加密组合（格式：密钥交换算法-对称加密算法-消息认证码算法）及生成的随机数 Client Random
- 服务端返回 ServerHello（选择的 TLS 版本、加密组合及生成的随机数 Server Random）、Certificate（服务端域名、CA 签名、公钥、证书有效期和证书链）、ServerKeyExchange（可选）和 CertificateRequest（可选，双向认证）
- 客户端验证服务端 Certificate，生成 Pre-Master Secret，发送 ClientKeyExchange（加密后的 Pre-Master Secret）、ChangeCipherSpec（切换加密模式）、Finished（验证握手完整性）、Certificate（可选，与服务端 CertificateRequest 匹配，双向认证）和 CertificateVerify（可选，双向认证）
- 服务端解密获取 Pre-Master Secret，验证客户端 Finished 消息及其身份（可选，双向认证），发送 ChangeCipherSpec（切换加密模式）和 Finished（验证握手完整性）
- 客户端接收、解密并验证服务端  Finished  消息，后续双方通过协商的加密算法通信
