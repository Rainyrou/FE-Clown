WebView 通过 Webkit 渲染引擎展示页面 view 组件，其为手机内置的高性能内核浏览器如 iOS 上的 WebKit，Android 上的 Chromium，在 SDK 中封装为一个组件，但不提供地址栏和导航栏，其可理解为页面 iframe，而 Native 代码则运行在操作系统环境中，Native 和 WebView 共用一套原生方法，通过 JSBridge 进行通信

- JavaScript 向 Native 发送信息，调用 Native 相关功能，通知 Native 当前 JavaScript 的相关状态等
- Native 向 JavaScript 发送信息，回溯调用结果，通知 JavaScript 当前 Native 的相关状态等

Hybrid 跨端 APP 使用 WebView 时出现跨域问题是由于 Web 浏览器和 WebView 均遵循同源策略，如请求后端 API、加载外部资源和嵌入不同域的 iframe 页面并与其进行通信

###### JavaScript 调用 Native

1. URL Scheme 拦截

JavaScript 向 WebView 发送一个带有特殊 Scheme 的请求如 `native://`，Native 端接收到该请求并解析指令，执行相应 Native 代码

- JavaScript：

```js
function callNative() {
  window.location.href = "native://actionName?param1=value1&param2=value2";
}
```

- iOS：在 UIWebView 或 WKWebView 中通过代理方法 `shouldStartLoadWithRequest` 拦截 URL 请求，解析 Scheme 和参数，执行相应 Native 代码

```objc
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
  NSURL *url = request.URL;
  if ([url.scheme isEqualToString:@"native"]) {
    // 解析 URL 并调用 Native 方法
    return NO;
  }
  return YES;
}
```

- Android：通过代理方法 `shouldOverrideUrlLoading` 拦截 URL 请求，执行相应 Native 代码

```java
@Override
public boolean shouldOverrideUrlLoading(WebView view, String url) {
  if (url.startsWith("native://")) {
    // 解析 URL 并调用 Native 方法
    return true;
  }
  return false;
}
```

2. JSBridge

为避免 URL Scheme 拦截方式的复杂性和效率问题，Hybrid 应用使用一种称为 JSBridge 的技术，JavaScript 运行于单独的 JS Context 中如 WebView 的 Webkit 引擎或 JSCore，其不会影响 WebView 的正常跳转行为，由于这些 Context 与 Native 运行环境的天然隔离，可将 JavaScript 和 Native 的每次互相调用看作一次 RPC 调用

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/195dddd1b07541fea10474bdb0804d51~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

在 JSBridge 的设计中，可将前端视为 RPC 客户端，将 Native 视为 RPC 服务端

1. 在 Native 端通过注入对象，将 Native 方法暴露给 JavaScript
2. JavaScript 调用暴露出来的方法，Native 端执行并返回结果

- JavaScript：

```js
window.NativeBridge.callNativeMethod('actionName', {param1: 'value1'}, function(response) {
  console.log(response);
});
```

- iOS：通过 WKWebView 的 `evaluateJavaScript` 方法将回调结果返回给 JavaScript，JavaScript 通过注入自定义对象调用 Native 接口

```objc
[webView evaluateJavaScript:@"window.NativeBridge.onResult('response data');" completionHandler:nil];
```

- Android：通过 `addJavascriptInterface` 方法将一个 Java 对象注入到 WebView 中并让 JavaScript 调用此对象的方法

```java
webView.addJavascriptInterface(new JavaScriptInterface(), "NativeBridge");

public class JavaScriptInterface {
  @JavascriptInterface
  public void callNativeMethod(String action, String params) {
    // 处理来自 JS 的调用
  }
}
```

3. PostMessage

新的通信方式，用于 WKWebView 和 Android WebView 的现代版本，通过 `window.postMessage` 实现与 Native 代码的异步通信，无需通过注入对象的方式

- JavaScript：

```js
window.webkit.messageHandlers.NativeBridge.postMessage({
  action: 'doSomething',
  params: {key: 'value'}
});
```

- iOS：用 `WKScriptMessageHandler` 接收 JavaScript 发送的请求

```js
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
  NSDictionary *params = message.body;
  // 处理消息
}
```

- Android：Android WebView 通过 `WebViewClient` 的 `onMessage` 方法接收 JavaScript 发送的请求

###### Native 调用 JavaScript

1. evaluateJavaScript

在现代的 WebView 中，iOS/Android 用 `evaluateJavaScript` 方法直接执行 JavaScript 代码

- iOS：

```objc
[webView evaluateJavaScript:@"javascriptFunction();" completionHandler:nil];
```

- Android：

```js
webView.evaluateJavascript("javascriptFunction();", null);
```

2. loadUrl

较老的 Android 版本用 `loadUrl` 执行 JavaScript 代码，效率较低

```js
webView.loadUrl("javascript:javascriptFunction()");
```

###### 小程序 AppService 双线程模型

开源社区的跨端框架：编译时 + 运行时
编译时：约定一套自己的 DSL，在编译打包时利用构建工具和 Babel 插件通过 AST 进行转译，早期 Taro 1.0 和 2.0 即采用此法，但其 Bug 较多且开发受限
运行时：小程序逻辑代码运行于 Vue & React 运行时，通过适配层实现自定义渲染器
Vue 跨端框架：魔改 Vue 框架，还是这一套响应式、虚拟 DOM、Diff 和模板编译等，而操作 DOM 节点部分的代码即框架新加的东西，即将原生 JS 操作 DOM 的方法替换成小程序 `setData`，一个 `.vue` 文件由 `template`、`script` 和 `style` 组成，其被编译并拆分为 `.wxml`、`.wxss`、`.js` 和 `.json`，`template` 部分需将 H5 标签替换为 `.wxml` 标签如 `<div>` 替换为 `<view>`，Vue 语法替换为小程序语法如 `v-if` 替换为 `wx:if`，模板替换即对齐 Vue 和小程序两端语法，将语法不一致的地方改为一致的，`script` 部分需将 `new Vue` 创建一个 Vue 实例替换成 `Page` 创建一个 Page 实例，`style` 部分直接将原生 CSS 部分迁移到 `.wxss`，小程序本身不支持原生操作 DOM 方法，因为其基于 PWA 的双层架构隔离逻辑进程和渲染进程，渲染层使用 WebView 渲染 UI，逻辑层使用 JSCore 执行 JavaScript，因此只能由 `setData` 实现由逻辑进程到渲染进程的更新，Vue 管理数据，小程序容器管理事件，跨端框架运行时 Native 作为桥梁，Vue 将序列化的数据同步到小程序上，而小程序容器捕获事件，调用在 Vue 注册时对应的事件处理程序

在小程序的架构中，WebView 和 AppService 的双线程模型用于分离小程序的 UI 渲染和业务逻辑以提升性能和用户体验，应对复杂的界面渲染和逻辑计算时可能出现的性能瓶颈

1. 逻辑层使用 JSCore 执行 JavaScript，AppService 线程负责所有业务逻辑、数据处理及与服务端通信，不直接操作 DOM，也不进行页面渲染
2. 渲染层使用 WebView 处理页面 UI 渲染和用户交互，WebView 线程通过 JavaScript 向 AppService 线程发送数据请求并等待 AppService 返回结果后更新页面

底层原理：

1. 线程分离
2. 通信机制：WebView 线程和 AppService 线程间通过异步消息传递机制（以消息队列的形式）来进行通信，而非直接访问对方数据
3. Native 作为桥梁：逻辑层和渲染层无法直接通信，通过 Native 层即微信客户端作为桥梁
4. JSON 序列化传输：为了在不同环境（JavaScriptCore 和 WebView）间传递数据，通信内容被序列化为 JSON 格式

全流程：

- WebView 线程进行用户交互如点击按钮或滚动页面，触发相应事件，将这些事件打包成消息，通过通信通道发送到 AppService 线程
- AppService 线程处理这些事件，执行相应业务逻辑如请求数据和计算结果等，执行完毕后，AppService 线程将结果返回给 WebView 线程
- WebView 线程接收到数据后更新页面

###### 判断 WebView 加载完成

1. onPageFinished 回调

iOS 和 Android 的 WebView 均提供用于监控页面加载过程的回调函数，其中最常用的是 `onPageFinished`

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

* 权限管理：通过签名验证或白名单机制严格控制哪些 APP 可访问 JS Bridge，避免滥用共享功能
* 数据加密：加密通过 JS Bridge 传递的数据，以防止中间人攻击或数据泄露
* 身份验证：在跨应用调用时，确保调用方经过身份验证，避免未经授权的 APP 访问敏感的 Native 服务

