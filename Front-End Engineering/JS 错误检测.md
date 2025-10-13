参考文章：[字节前端监控实践](https://juejin.cn/post/7195496297150709821)

| 错误类型                | 触发场景                | 捕获方式                  |
| ------------------- | ------------------- | --------------------- |
| `SyntaxError`       | 语法解析失败              | 全局 error 事件           |
| `TypeError`         | 数据类型错误              | 全局 error 事件           |
| `ReferenceError`    | 变量引用未定义             | 全局 error 事件           |
| `Promise Rejection` | 未处理的 reject Promise | unhandledrejection 事件 |


1. 全局错误捕获机制：通过 `ErrorBoundary` 和 `errorHandler` 全局捕获 React/Vue 组件渲染错误并返回兜底 UI，`window.addEventListener` 分别监听 `error` 和 `unhandledrejection` 事件捕获同步（未被捕获的全局运行时错误）和异步错误（未被处理的 rejected Promise），同步错误携带完整堆栈信息，而异步错误由于浏览器事件循环机制导致其无法沿调用栈回溯其在创建时的堆栈信息，因此监控 SDK 以  `try/catch` 包装部分全局异步 API 和全局事件 API，捕获 JavaScript 错误时劫持异步 API 注入异步错误堆栈上下文并抛出错误

```js
window.addEventListener("error", (event) => {
  report({
    type: event.error.name,
    message: event.message,
    stack: event.error.stack,
    file: event.filename,
    line: event.lineno,
    col: event.colno,
  });
});

window.addEventListener("unhandledrejection", (event) => {
  report({
    type: "Promise Rejection",
    reason: event.reason,
  });
});
```

通过 `User-Agent` 采集浏览器和操作系统环境信息：

```js
const envInfo = {
  ua: navigator.userAgent,
  os: getOS(),
  browser: getBrowser(),
  screen: `${screen.width}x${screen.height}`,
  location: window.location.href,
  network: navigator.connection?.effectiveType,
};
```

用户行为回溯：

```js
const actionStack = [];

document.addEventListener("click", (e) => {
  actionStack.push({
    type: "click",
    target: e.target.tagName,
    time: Date.now(),
  });
});

function report(error) {
  sendToServer({
    ...error,
    actions: actionStack.slice(-10), // 上报最近 10 个操作
  });
}
```

劫持异步 API 注入异步错误堆栈上下文：

```js
const originalSetTimeout = window.setTimeout;
window.setTimeout = (callback, delay) => {
  const stack = new Error().stack; // 捕获调用时的堆栈
  return originalSetTimeout(() => {
    try {
      callback();
    } catch (e) {
      e.stack += `\nAsync Context: ${stack}`;
      throw e;
    }
  }, delay);
};
```

2. 堆栈反解与聚合策略：线上环境代码由于兼容编译和压缩打包与开发环境代码存在较大差异，构建时上传 Source Map 文件，建立压缩代码与源代码的映射关系，通过 `mozila/source-map` 库反向解析线上 JavaScript 错误堆栈，定位压缩代码位置即错误堆栈中的  `bundle.js:i:j`，Base64 VLQ 解码 `mappings`  字段查找对应源文件位置，还原原始堆栈，将  `bundle.js` 第 i 行 j 列映射到  `src/index.ts` 第 x 行 y 列，在聚合算法中基于 `name` 错误类型、`message` 消息及反解后的 `stacktrace` 堆栈帧生成 `fingerprint` 唯一指纹，如 Sentry 的堆栈拆分策略即提取堆栈帧中的函数名、文件名和代码行，通过哈希计算生成 `GroupingComponent`，确保相同逻辑错误聚合为同一 Issue，再根据错误影响用户数（如 >5）过滤偶发噪声，避免无效报警，以代码版本判断不同发布时期的错误

```js
const rawSourceMap = fs.readFileSync('bundle.js.map');
const consumer = await new SourceMapConsumer(rawSourceMap);
const originalPos = consumer.originalPositionFor({ line: 12, column: 34 });
```

![[Pasted image 20250330210649.png]]
