[WebSocket 教程 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2017/05/websocket.html)

[WebSocket - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)

[WebSocket - Wikipedia](https://en.wikipedia.org/wiki/WebSocket)

- HTTP 是一个请求-响应协议，即通信始终是由客户端发起，客户端发送一个请求到服务端，服务端处理该请求并返回一个响应，在此过程中，服务端无法主动向客户端推送数据。因此，当需要服务端实时向客户端推送信息时，HTTP 表现得并不理想
- 为解决这一问题，我们只能使用轮询 Polling，客户端定期向服务端发送请求，询问是否有新信息生成，若有，服务端将信息作为响应返回给客户端，否则返回一个空响应。轮询非常浪费资源。因为在大部分时间里，请求的响应都是空的，客户端和服务端间大量无效通信导致不必要的服务端压力
* WebSocket 正是为解决这一问题而设计的，与 HTTP 不同，WebSocket 提供一个持久的全双工通信通道，一旦建立 WebSocket 连接，客户端和服务端即可在单一持久连接上通信，它的连接是持久的，一直保持打开状态，直到客户端或服务端明确关闭连接。WebSocket 建立在 TCP 之上，服务端的实现相对容易。WebSocket 连接建立需客户端和服务端间进行一次握手，握手成功后，数据不再通过 HTTP 传输，而是通过已建立的 WebSocket 连接传输，无需额外头信息，数据格式较轻量，性能开销小。在 WebSocket 中，服务端可主动向客户端发送信息，而无需客户端先发起请求，这使得 WebSocket 适用于那些需要服务端实时客户端推送数据的应用场景，如聊天室、在线游戏、实时交易系统等。它与 HTTP 有着良好的兼容性，默认端口也是 80 和 443，且握手阶段采用 HTTP，因此握手时不易屏蔽，它不受同源策略限制，能通过各种 HTTP 代理服务端，穿透大多数防火墙，它可以发送文本和二进制数据。其标识符是 ws，若加密，则为 wss，服务端网址即 URL，如 `ws://example.com:80/some/path`
* 如果要指定多个回调函数，可使用 `addEventListener` 方法

1. `addEventListener` 是一个通用的方法，用于在一个特定的 DOM 对象上注册一个事件处理器。WebSocket 对象是一个 `EventTarget`，所以它可以使用 `addEventListener` 方法
2. `addEventListener` 的第一个参数是一个字符串，代表你想监听的事件名。第二个参数是一个函数，当指定的事件触发时，这个函数会被执行

```JavaScript
const ws = new WebSocket('ws://example.com/socket');

ws.addEventListener('open', (event) => {
	ws.send('Hello Server!!!');
});

ws.addEventListener('open', (event) => {
	console.log('Connection opened');
});
```

_**客户端**_

```JavaScript
let socket = new WebSocket('ws://clown.com/socketserver');

socket.onopen = (event) => {
	console.log('WebSocket connection opened:', event);
	socket.send('Hello Server!!!');
}

socket.onmessage = (event) => {
	console.log('Message from server:', event.data);
}

socket.onclose = (event) => {
	console.log('WebSocket connection closed:', event);
}

socket.onerror = (event) => {
	console.log('WebSocket error:', event);
}
```

_**服务器端**_

```JavaScript
const WebSocket = require('ws');
const ws = new WebSocket.Server({ post: 8080 });

ws.on('connection', connection(ws) => {
	ws.on('message', incoming(message) => {
		console.log('Received', message);
	});
	ws.send('Hello Client!!!');
});
```
