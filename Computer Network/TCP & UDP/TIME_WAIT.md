TIME_WAIT 状态：四次挥手的最后阶段，客户端接收到服务端的最后一个 FIN 报文后，向服务端返回 ACK 应答报文，客户端进入 TIME_WAIT 状态，在此状态下，客户端等待 2 MSL 后断开连接。Maximum Segment Lifetime 是任何报文段被丢弃前在网络内的最长时间，RFC 793 定义的标准 MSL 为 2 分钟，因此 TIME_WAIT 状态的持续时间是 4 分钟

###### TIME_WAIT 状态的原因

1. 确保最后一个 ACK 报文能够到达：若客户端的最后一个 ACK 报文丢失，服务端将重传最后的 FIN 报文，TIME_WAIT 状态确保客户端有机会重新发送 ACK 报文响应重传的 FIN 报文
2. 等待足够时间以确保网络中所有报文均已到期：防止延迟的报文段在相同的四元组上之新连接中被错误接受
