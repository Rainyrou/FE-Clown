- 内核管理：在 Linux 中，TCP 等网络协议栈均由内核管理，内核负责分配和管理每个 socket 使用的内存，包括发送缓冲区和接收缓冲区
- 缓冲区：TCP 的性能和内存使用密切相关，因为 TCP 使用缓冲区来存储待发送及已接收但未被应用读取的数据，缓冲区大小可通过 socket 选项调整
- 内存压力和释放：当系统内存紧张时，内核尝试减少为 socket 分配的内存，影响 TCP 连接的性能，而当 TCP 连接关闭时，相关的内存资源被释放回系统

在 Linux 系统中，`/proc/net/sockstat` 和 `/proc/net/sockstat6` 文件提供关于 socket 使用情况的统计信息，包括 TCP、UDP 等协议的 socket。通过查看这些文件，可获取到当前系统级别的 socket 使用情况，包括打开的 socket 数量、内存使用等

```bash
cat /proc/net/sockstat
cat /proc/net/sockstat6
```

`ss` 和 `netstat` 用于查看系统中当前所有 TCP 连接的状态

```bash
ss -tm
```

显示每个连接的内存使用情况 `-m` 和定时器信息 `-t`

```bash
netstat -tan
```

`/proc/[pid]/status` 文件提供特定进程的内存使用情况，其中 `[pid]` 是进程的 ID

```bash
cat /proc/[pid]/status
```

