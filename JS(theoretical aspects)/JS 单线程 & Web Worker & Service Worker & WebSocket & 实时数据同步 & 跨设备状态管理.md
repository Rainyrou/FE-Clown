JavaScript 设计之初是为浏览器提供轻量级页面交互功能，当时的 Web 页面无需复杂业务逻辑，单线程足以应付绝大多数业务场景。而在多线程环境中，并发访问共享资源需复杂的同步机制如一个线程修改某 DOM 元素，而另一线程删除同一 DOM 元素，此类问题虽可通过锁等方式解决，但却引入死锁和性能损耗等新问题，因此 JavaScript 在设计之初通过单线程的形式简化前端开发，无需考虑多线程并发和数据一致性等问题。JavaScript 的执行环境通过事件循环机制支持异步编程，这使得 JavaScript 能够执行非阻塞 I/O 操作如网络请求和文件操作等。尽管 JavaScript 单线程，但现代 JavaScript 已发展出多种方法来模拟多线程处理如 Web Worker 和 Service Worker，Web Worker 为浏览器中独立于主线程运行的线程，有其独立的上下文、全局对象、事件循环和生命周期，可执行计算密集或时间长的任务而避免阻塞主线程；Service Worker 为浏览器中独立于主线程运行的线程，用于实现离线支持、后台同步和推送通知等

Web Worker：

1. 线程安全：Web Worker 为浏览器中独立于主线程运行的线程，有其独立的上下文、全局对象、事件循环和生命周期，无法直接访问和操作 DOM、`window`  对象或主线程中的全局变量
2. 生命周期：

创建 -> 调用  `new Worker(url)`  实例化，浏览器下载并执行指定脚本
销毁 -> 主线程调用  `worker.terminate` 强制销毁或页面关闭时自动销毁

3. 消息通信：主线程与 Web Worker 通过  `postMessage` 和 `onmessage`  双向通信，浏览器使用结构化克隆算法深拷贝数据

Service Worker：

1. 生命周期 & 请求拦截 & 缓存策略：Service Worker 基于 Web Worker 模型，用于实现离线支持、后台同步和推送通知等，其为浏览器中独立于主线程运行的线程（通过 postMessage 通信），无 DOM 访问权限，有其独立的事件循环和生命周期，包括安装、等待、激活和销毁，浏览器在主线程中通过 `navigator.serviceWorker.register` 注册 Service Worker 并持久化存储，在安装阶段 Service Worker 触发  `install`  事件以预缓存关键资源，新版本安装完后进入等待阶段，直到旧版本控制的页面全部关闭，可通过  `self.skipWaiting`  强制激活，在控制权转移时触发  `activate`  事件以清理旧缓存并升级版本，而后 Service Worker 通过监听  `fetch`  事件拦截同作用域内的所有请求，通过 Cache API 存储静态资源或预缓存数据，当拦截到请求时，可直接返回缓存中的响应或发送请求并缓存新响应或通过自定义策略如网络优先、缓存优先或离线回退等以响应请求，这种拦截—缓存—返回的机制使得页面在网络不佳或离线的情况下仍正常运行
2. 唤醒机制：Service Worker 通过事件监听器如 `fetch`、`push` 和 `sync` 等响应异步事件，当事件触发时，Service Worker 被唤醒，处理完后等待下一事件
3. 安全机制：Service Worker 脚本必须与页面同源，可通过  `Service-Worker-Allowed`  头部扩展其作用域，而在生产环境必须使用 HTTPS，防止 XSS 攻击篡改 Service Worker 脚本

WebSocket：基于 TCP，默认端口为 80 和 443，其不受同源策略限制，提供持久化双向实时通信通道，建立连接只需客户端和服务端一次 HTTP 握手，而后通过 WebSocket 连接通信，数据传输采用二进制帧格式且客户端发送的数据需通过  `Masking-Key`  进行掩码处理

1. 客户端首次发送的 GET 请求中 `Upgrade: websocket`  和  `Connection: Upgrade` 表示协议升级为 WebSocket

```http
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
```

2. 服务端返回 101 状态码，确认协议升级：

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

3. 心跳机制：由客户端和服务端某一方主动发送 Ping 帧以检测连接是否有效，而接收方接收到 Ping 帧后返回内容完全相同的 Pong 帧，若发送方未接收到 Pong 帧，可主动关闭连接

在页面加购、手机结算的场景下，保持数据一致且不刷新界面：购物车数据存储于服务端，而客户端仅作为视图/缓存，任何客户端上的增删改操作实时向服务端发送请求，更新对应数据，而服务端数据更新后，向所有活跃的客户端发送数据变化的请求，客户端接收到请求后，更新本地购物车数据，为保持多端实时同步，通过 WebSocket 提供持久化双向实时通信通道，通过 JWT/Cookie/Session 确保不同设备访问同一用户的购物车数据并通过本地存储监听或内存状态管理实现数据实时更新
