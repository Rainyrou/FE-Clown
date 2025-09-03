WebView 为移动设备操作系统提供的嵌入式浏览器渲染引擎组件，其封装移动设备内置的高性能浏览器内核如 iOS WebKit，Android Chromium，其不提供地址栏、导航栏和书签管理器，在功能上是一个由 Native 代码创建和控制、用于展示 Web 内容的容器视口，可类比为页面中的  **`<iframe>`**  标签，但权限和集成度远高于后者，WebView 运行于受控沙箱，而 Native 代码则运行于操作系统，WebView 和 Native 共用一套原生方法，通过 JSBridge 进行异步通信

- JavaScript 通过拦截 URL Scheme 或执行由 Native 注入的全局对象上的方法向 Native 发送请求，Native 拦截请求、解析 URL Scheme 或全局对象的参数和方法并执行相应 Native 代码
- Native 通过调用 WebView API 直接在 WebView 的上下文中执行 JavaScript 并通过回调机制获取执行结果

Hybrid 跨端 APP 使用 WebView 时出现跨域问题是由于 Web 浏览器和 WebView 均遵循同源策略，如请求后端 API、加载外部资源和嵌入不同域的 iframe 页面并与其进行通信

###### 判断 WebView 加载完成

1. onPageFinished

iOS 和 Android 的 WebView 均提供用于监控页面加载过程的回调函数如 `onPageFinished`

Android：

```java
WebView webView = new WebView(context);
webView.setWebViewClient(new WebViewClient() {
    @Override
    public void onPageFinished(WebView view, String url) {
        // 页面加载完成
        super.onPageFinished(view, url);
        Log.d("WebView", "Page loaded completely");
    }
});
```

2. onPageStarted

```java
webView.setWebViewClient(new WebViewClient() {
    @Override
    public void onPageStarted(WebView view, String url, Bitmap favicon) {
        // 页面开始加载
        super.onPageStarted(view, url, favicon);
        Log.d("WebView", "Page started loading");
    }

    @Override
    public void onPageFinished(WebView view, String url) {
        // 页面加载完成
        super.onPageFinished(view, url);
        Log.d("WebView", "Page loaded completely");
    }
});
```

3. 加载进度

Android 提供 `WebChromeClient` 的 `onProgressChanged` 回调方法，用于跟踪页面加载的实时进度，当加载进度达到 100% 时，可认为页面加载完毕

```java
webView.setWebChromeClient(new WebChromeClient() {
    @Override
    public void onProgressChanged(WebView view, int newProgress) {
        super.onProgressChanged(view, newProgress);
        if (newProgress == 100) {
            // 页面加载完成
            Log.d("WebView", "Page loading complete with progress 100%");
        }
    }
});
```

###### JS Bridge 跨多 APP 共用

JS Bridge 能跨多 APP 共用是由于其通信机制的设计和实现方式，其依赖操作系统提供的进程间通信机制 IPC，可在多个应用间共享相同的接口和通信方法，甚至可互相调用对方的 Native 功能

1. IPC

- Android 提供 Binder IPC 机制，用于不同应用间通过 IPC 共享资源如多个 APP 注册相同的 JS Bridge 服务，应用间通过 Binder 机制通信，互相调用 Native 功能。
- iOS 通过 URL Scheme 或 Universal Links 实现跨应用通信并通过自定义的通信协议来传递数据

2. 统一的协议和接口标准

- 每个 APP 均实现相同的 JS Bridge 协议，使得 WebView 中的 JavaScript 能以相同的方式调用 Native 功能
- 通过 URL Scheme 或指定的协议格式，JavaScript 可在不同的 APP 中发送相同请求并接收到相同的返回结果
- JS Bridge 接口采用标准的 JSON 格式封装数据

3. 通过 WebView 和共享组件 SDK 实现跨应用通信

- 多个 APP 使用相同的 WebView 组件或 SDK，这些 APP 可共享同一 JS Bridge 实现
- JS Bridge 有时并不是直接通过本地通信实现，而通过云服务来实现跨应用的功能调用，WebView 中的 JavaScript 发送请求到云端，云端再通过服务调用不同 APP 的 Native 功能

4. 共享 Native 功能和服务

若多个 APP 由同一家公司开发，它们可能共享一些底层 Native 服务如登录和支付服务等

- 共享登录服务：在同一生态系统中，多个 APP 可共享统一登录功能，当用户在其中一个 APP 登录后，其他 APP 也可通过 JS Bridge 调用该登录状态
- 共享支付服务：多个 APP 可共享统一的支付宝或微信支付接口，开发者只需实现一次支付流程，多个应用即可通过 JS Bridge 直接调用该支付功能

5. 安全考量

跨应用通信时若无严格的权限控制和身份验证，可能导致恶意 APP 通过 JS Bridge 调用其他应用的敏感功能

- 权限管理：通过签名验证或白名单机制严格控制哪些 APP 可访问 JS Bridge，避免滥用共享功能
- 数据加密：加密通过 JS Bridge 传递的数据，以防止中间人攻击或数据泄露
- 身份验证：在跨应用调用时，确保调用方经过身份验证，避免未经授权的 APP 访问敏感的 Native 服务
