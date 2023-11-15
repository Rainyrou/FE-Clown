Web Workers 提供了一种在 Web 应用中运行脚本的方式，而不会干扰主线程的用户界面。它们通常用于执行耗时的计算，以避免阻塞 UI。Web Workers 可通过特定的消息传递机制将计算结果返回给主线程

Web Workers 与主线程的通信基于事件的消息传递机制。具体来说，使用 `postMessage` 方法和 `onmessage` 事件处理程序来进行通信

1. 主线程

* 在主线程中，创建一个 Worker 对象，并指定要在 Worker 线程中运行的脚本文件。然后在主线程中为该 Worker 设置一个 `onmessage` 事件处理程序，用于接收从 Worker 返回的数据

```JavaScript
var myWorker = new Worker('worker.js');

myWorker.onmessage = function(e) {
  console.log('Message received from worker:', e.data);
};
```

2. Worker 线程

* 在 Worker 线程（通常是 `worker.js` 文件）中使用 `postMessage` 方法返回数据给主线程

```JavaScript
var result = performSomeCalculation(); 
postMessage(result); 
```

###### 示例

`index.html`

```JavaScript
<script>
var myWorker = new Worker('worker.js');

myWorker.onmessage = function(e) {
  console.log('Message received from worker:', e.data);
};

myWorker.postMessage('start calculation');
</script>
```

`worker.js`

```JavaScript
onmessage = function(e) {
  if (e.data === 'start calculation') {
    var result = performSomeCalculation(); 
    postMessage(result); 
  }
};

function performSomeCalculation() {
  return 'calculation result';
}
```

###### 注意事项

- 由于 Web Workers 运行在与主线程隔离的环境中，它们无法直接访问 DOM
- 在与 Web Workers 通信时传输的数据是拷贝的，而不是通过引用共享的
- 对于复杂的数据结构，考虑使用 `Transferable` 对象（如 `ArrayBuffer`）进行更高效地传输数据，因为它们是通过引用共享而不是拷贝来进行传输的
