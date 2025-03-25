Ping 基于 ICMP 协议（网络层），用于检测网络设备间的连通性、延迟和丢包情况

底层原理：

1. 源主机构造  ICMP Echo Request 报文（Type=8），包含序列号和时间戳，通过 IP 层发送至目标主机

```
| IP Header | ICMP Header (Type=8) | Payload (Data) |
```

2. 目标主机接收到报文后，若网络正常且未被防火墙拦截，生成 ICMP Echo Reply 报文（Type=0），原样返回 Payload
3. 源主机接收到报文后，根据发送和接收时间差得出延迟，计算 RTT 往返时间，并通过发送次数与应答次数计算丢包率
