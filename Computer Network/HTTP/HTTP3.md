1. HTTP3 也采⽤⼆进制帧的结构，但 HTTP2 的二进制帧需定义 Stream，⽽ HTTP3 自身无需定义 Stream，直接使⽤ QUIC ⾥的 Stream，因此 HTTP3 的帧结构简单，只含有类型和长度，根据帧类型分为数据帧和控制帧，而 HEADERS 帧和 DATA 帧属于数据帧

![[Pasted image 20240424172105.png]]

2. HTTP3 在头部压缩算法上从 HTTP2 的 HPACK 升级为 QPACK，QPACK 也采用静态表、动态表及 Huffman 编码
3. Quick UDP Internet Connections 由 Google 开发，其基于 UDP，但提供类似于 TCP 的可靠性，用于改进 HTTP/2

- 建立连接：减少连接建立所需的往返次数 RTT，新的 QUIC 整合加密和连接，只需一个 RTT 即可完成握手
- 多路复用：支持多路复用并解决 HTTP2 队头阻塞问题，一个连接中的丢包对其他流不产生影响
- 前向纠错：支持前向纠错机制，无需重新传输即可恢复丢包
- 0-RTT 恢复：由一个连接 ID 标识，而非传统的 IP 地址 + 端口组合，即使客户端 IP 改变，再次连接时使用之前的加密参数，实现无缝迁移，有利于移动设备切换网络