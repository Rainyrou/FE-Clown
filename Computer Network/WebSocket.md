[WebSocket 教程 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2017/05/websocket.html)

[WebSocket - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)

[WebSocket - Wikipedia](https://en.wikipedia.org/wiki/WebSocket)

- HTTP 是一个请求-响应协议，即通信始终是由客户端发起的。客户端发送一个请求到服务器，服务器处理该请求并返回一个响应。在这个过程中，服务器无法主动向客户端推送数据。因此，当需要服务器实时地向客户端推送信息时，HTTP 协议表现得并不理想
- 为解决这一问题，我们只能使用"轮询"（Polling），客户端定期向服务器发送请求，询问是否有新信息。如果服务器有新信息，它将信息作为响应返回给客户端；否则返回一个空的响应
* 轮询的问题是非常浪费资源。因为在大部分时间里，请求的回应都是空的，客户端和服务器之间大量无效的通信导致不必要的网络拥塞和服务器负担
* WebSocket 协议为解决这个问题而被设计。与 HTTP 不同，WebSocket 提供一个持久的、全双工的通信通道，一旦 WebSocket 连接被建立，客户端和服务器就可以在一个单一的持久连接上相互发送信息
* 在 WebSocket 中，服务器可以主动向客户端发送消息，而不需要客户端先发起请求。这使得 WebSocket 非常适合那些需要服务器实时向客户端推送数据的应用场景，如聊天室、在线游戏、实时交易系统等
* WebSocket 建立在 TCP 协议之上，服务器端的实现比较容易
* 与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器，穿透大多数防火墙
* WebSocket 的连接建立过程需要客户端和服务器之间进行一次握手。握手成功后，数据就不再通过 HTTP 协议传输，而是通过已建立的 WebSocket 连接传输，不需要其他额外的头信息。数据格式比较轻量，性能开销小，通信高效
* WebSocket 不仅可以发送文本数据，还支持发送二进制数据
* WebSocket 的连接是持久的，它会一直保持打开状态，直到客户端或服务器明确地关闭连接
* WebSocket 协议本身不实施同源策略。换句话说，使用 WebSocket，你可以从任何网站连接到任何服务器和端口，而不必担心跨域问题
* WebSocket 协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL，如 `ws://example.com:80/some/path`


* 如果要指定多个回调函数，可以使用 `addEventListener` 方法

1. `addEventListener` 是一个通用的方法，用于在一个特定的 DOM 对象上注册一个事件处理器。WebSocket 对象是一个 `EventTarget`，所以它可以使用 `addEventListener` 方法
2. `addEventListener` 的第一个参数是一个字符串，代表你想监听的事件名。第二个参数是一个函数，当指定的事件触发时，这个函数会被执行

```JavaScript
// 创建 WebSocket 连接
const ws = new WebSocket('ws://example.com/socket');

// 定义第一个连接成功后的回调函数
ws.addEventListener('open', (event) => {
	ws.send('Hello Server!!!');
});

// 定义第二个连接成功后的回调函数 
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

// 用于指定收到服务器数据后的回调函数
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


