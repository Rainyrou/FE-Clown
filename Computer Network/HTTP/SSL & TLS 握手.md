1. ClientHello：客户端发送一个 ClientHello 给服务端，其包含客户端支持的 TLS 版本，可接受的密码套件（密钥交换算法、对称加密算法、消息认证码算法）及客户端生成的随机数 Client Random
2. ServerHello：服务端返回一个 ServerHello，从客户端提供的选项中选择 TLS 版本和密码套件，并提供服务端生成的随机数 Server Random 和证书，必要时还提供 ServerKeyExchange，提供更多加密相关的参数
3. ServerHelloDone：服务端发送 ServerHelloDone，表示服务端 Hello 阶段结束
4. 客户端响应：若服务端请求认证，则客户端发送证书，客户端发送 ClientKeyExchange，必要时还提供证书验证消息以验证其证书的有效性，接着它发送 ChangeCipherSpec 和 Finished，告诉服务端后续消息使用之前协商的密码套件进行加密，Finished 包含前面消息的校验值，以验证握手过程的完整性
5. 服务端响应：服务端发送 ChangeCipherSpec 和 Finished，Finished 包含前面消息的校验值，以验证握手过程的完整性
6. 加密传输：一旦客户端和服务端交换 Finished，即完成 TLS 握手，双方使用选定的加密算法和密钥进行加密数据传输
