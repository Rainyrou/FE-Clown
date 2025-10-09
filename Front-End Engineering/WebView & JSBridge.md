WebView 为移动设备操作系统提供的嵌入式浏览器渲染引擎组件，其封装移动设备内置的高性能浏览器内核如 iOS WebKit，Android Chromium，其不提供地址栏、导航栏和书签管理器，在功能上是一个由 Native 代码创建和控制、用于展示 Web 内容的容器视口，可类比为页面中的  **`<iframe>`**  标签，但权限和集成度远高于后者，WebView 运行于受控沙箱，而 Native 代码则运行于操作系统，WebView 和 Native 共用一套原生方法，通过 JSBridge 进行异步通信

- JavaScript 通过拦截 URL Scheme 或执行由 Native 注入的全局对象上的方法向 Native 发送请求，Native 拦截请求、解析 URL Scheme 或全局对象的参数和方法并执行相应 Native 代码
- Native 通过调用 WebView API 直接在 WebView 的上下文中执行 JavaScript 并通过回调机制获取执行结果

Hybrid 跨端 APP 使用 WebView 时出现跨域问题是由于 Web 浏览器和 WebView 均遵循同源策略，如请求后端 API、加载外部资源和嵌入不同域的 iframe 页面并与其进行通信

判断 WebView 加载完成：

1. iOS 和 Android WebView 监控页面加载过程的回调函数如 `onPageFinished/onPageStarted`
2. iOS 和 Android WebView 监控页面加载实时进度的回调函数如 `onProgressChanged`

JS Bridge 跨多 APP 共用：通信机制的设计和实现（IPC/URL Scheme + 统一协议和接口标准 + WebView 和共享组件 SDK + 共享 Native 功能和服务）

