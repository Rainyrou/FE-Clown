Inline Frame 为 HTML 元素，其通过创建独立 DOM 和 JavaScript 环境，允许在某页面中显示其他页面内容，与原页面保持交互独立，若 iframe 与外部页面同源，则两者直接通过 JavaScript 访问彼此的全局变量、函数和 DOM 等，若 iframe 与外部页面跨域，通过 `postMessage` 和 `onmessage` 跨域通信

```html
<iframe src="https://example.com" width="600" height="400" title="example" sandbox="allow-scripts allow-same-origin"
  loading="lazy"></iframe>
```

1. 通过  `sandbox`  属性限制 Iframe 行为

- `allow-same-origin`：同源策略
- `allow-scripts`：执行脚本
- `allow-forms`：提交表单
- `allow-popups`：打开新窗口

2. 模块化开发
3. 各个 Iframe 有其独立渲染进程，导致页面卡顿

Web Worker：

1. 线程安全：Web Worker 为浏览器中独立于主线程运行的线程，有其独立的上下文、全局对象、事件循环和生命周期，无法直接访问和操作 DOM、`window`  对象或主线程中的全局变量
2. 生命周期：

创建 -> 调用  `new Worker(url)`  实例化，浏览器下载并执行指定脚本
销毁 -> 主线程调用  `worker.terminate` 强制销毁或页面关闭时自动销毁

3. 消息通信：主线程与 Web Worker 通过  `postMessage` 和 `onmessage`  双向通信，浏览器通过结构化克隆算法深拷贝数据

Web Worker 本地开发：

本地开发的问题在于直接打开本地 HTML 文件时，浏览器因同源策略限制拦截 Web Worker 脚本加载，`file://`  为本地文件访问协议而非网络协议，而浏览器由于安全限制默认禁止  `file://`  协议下加载 Web Worker 脚本，此外部分浏览器将  `file://`  协议下的脚本加载视为跨域请求，因此本地开发必须移除  `file://`  协议，通过 Node.js 内置 HTTP 模块搭建本地服务端，将项目目录作为静态资源根目录，服务端监听本地端口，让 HTML（index.html） 和 Web Worker（worker.js） 脚本均通过  `https://localhost:8080`  访问

Service Worker：

* 生命周期 & 请求拦截 & 缓存策略：Service Worker 基于 Web Worker 模型，用于实现离线支持、后台同步和推送通知等，其为浏览器中独立于主线程运行的线程（通过 postMessage 通信），无 DOM 访问权限，有其独立的事件循环和生命周期，包括安装、等待、激活和销毁，浏览器在主线程中通过 `navigator.serviceWorker.register` 注册 Service Worker 并持久化存储，在安装阶段 Service Worker 触发  `install`  事件以预缓存关键资源，新版本安装完后进入等待阶段，直到旧版本控制的页面全部关闭，可通过  `self.skipWaiting`  强制激活，在控制权转移时触发  `activate`  事件以清理旧缓存并升级版本，而后 Service Worker 通过监听  `fetch`  事件拦截同作用域内的所有请求，通过 Cache API 存储静态资源或预缓存数据，当拦截到请求时，可直接返回缓存中的响应或发送请求并缓存新响应或通过自定义策略如网络优先、缓存优先或离线回退等以响应请求，这种拦截—缓存—返回的机制使得页面在网络不佳或离线的情况下仍正常运行
* 唤醒机制：Service Worker 通过事件监听器如 `fetch`、`push` 和 `sync` 等响应异步事件，当事件触发时，Service Worker 被唤醒，处理完后等待下一事件
* 安全机制：Service Worker 脚本必须与页面同源，通过  `Service-Worker-Allowed`  头部扩展其作用域，而在生产环境必须使用 HTTPS 以防止 XSS 攻击

WebSocket：基于 TCP，默认端口为 80 和 443，其不受同源策略限制，提供持久化双向实时通信通道，通过二进制帧格式传输数据

- TCP 可靠传输
- WebSocket 协议层增强：建立连接只需客户端和服务端一次 HTTP 握手，而后通过 WebSocket 连接通信，客户端发送  `Upgrade: websocket` + `Sec-WebSocket-Key`  请求头部，服务器返回  `101 Switching Protocols` + 加密后的  `Sec-WebSocket-Accept`  响应头部，发送方发送关闭帧和关闭码，接收方返回 ACK 报文后关闭连接
- 心跳机制：由客户端和服务端某一方主动发送 Ping 帧以检测连接是否有效，而接收方接收到 Ping 帧后返回内容完全相同的 Pong 帧，若发送方未接收到 Pong 帧，则判断连接失效，主动关闭连接
- 重连机制：指数退避重连 + 随机抖动 + 会话状态恢复 + 重连超时终止

实时数据同步 + 跨设备状态管理：数据实际存储于后端，前端增删改查并通过 WebSocket 连接实时发送请求给后端以更新相应数据，后端数据更新后，监听数据变化的所有前端更新本地数据，通过 JWT/Cookie/Session 确保同一用户在不同机型设备下的状态一致

Server-Sent Events：单向实时通信，客户端通过 EventSource 接口向服务端发送 GET 请求，请求头部字段 `Accept: text/event-stream`，服务端接收到请求后持续保持连接状态并向客户端主动推送数据，返回响应头部字段 `Content-Type: text/event-stream + Connection: keep-alive + Cache-Control: no-cache + Transfer-Encoding: chunked`

轮询："请求 - 响应 - 关闭"模式，客户端定期（`setTimeout` 和 `setInterval`）向服务器发送请求，询问是否有新数据可用，服务端接收到请求后立即处理请求并返回响应（无论是否有新数据），基于 HTTP 短连接机制，每次请求响应完成后立即关闭 TCP 连接

长轮询："请求 - 挂起 - 响应 - 重新请求" 模式，服务端接收到请求后，若无新数据则不立即返回响应，而将请求置于挂起状态，持续保持 HTTP 连接状态并监听数据变化（定时轮询/消息队列/数据库触发器），检测到新数据或超时才返回响应并关闭连接
