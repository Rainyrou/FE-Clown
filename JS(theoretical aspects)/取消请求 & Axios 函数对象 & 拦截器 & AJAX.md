1. Axios：通过 `CancelToken` 创建取消令牌，在发送请求时携带，通过调用 `cancel` 取消请求
2. XMLHttpRequest：通过调用 `XMLHttpRequest.abort` 取消请求
3. Fetch：通过 `AbortController` 创建实例信号，在发送请求时携带，当信号触发时通过 `abort` 取消请求
4. WebSocket：通过调用 `WebSocket.close` 取消请求

###### 函数 & 对象

在 JavaScript 中函数也是对象，函数像其他对象一样拥有自己的属性和方法，Axios 是一个基于 Promise 的 HTTP 客户端，用于浏览器和 Nodejs 中，其设计允许它既可以作为函数调用，也可以作为对象使用。当 Axios 作为函数调用时如 `axios(config)`，实际上是在调用 `Axios.prototype.request` 方法，其接受一个配置对象 `config` 作为参数。当 Axios 作为对象使用时，其提供多种 HTTP 方法的快捷方式如 `.get`、`.post`、`.delete` 等，它们封装 `request` 方法并预设请求的方法类型

1. 创建实例：Axios 初始化时自动创建一个 Axios 实例，其配置默认的请求参数如 baseURL、headers 等，也可通过 `axios.create(config)` 手动创建新的实例，以便为不同请求配置相应参数
2. 处理配置：Axios 处理和合并配置对象，如合并全局 Axios 实例的默认配置和当前请求的特定配置，处理请求拦截器和响应拦截器等
3. 请求拦截器：在请求发送前，Axios 遍历并执行所有注册的请求拦截器，请求拦截器处理请求数据，如设置统一的请求头、添加 token 等
4. 发送请求：当调用 `axios` 或其方法如 `axios.get` 时，实际上是在 `axios(config)` -> 创建一个请求的配置对象或 `axios.get(url, config)` -> 将参数传递给相应方法，该配置对象包含 URL、方法等。Axios 使用底层的 HTTP 客户端如浏览器的 XMLHttpRequest 对象或 Node.js 的 HTTP 模块发送异步请求，返回一个 Promise 对象，其将配置对象转换为客户端理解的请求格式，并处理如设置正确的请求头、转换请求体等
5. 响应拦截器：在响应数据返回给调用者前，Axios 逆序遍历并执行所有注册的响应拦截器，响应拦截器处理响应数据，如进行数据转换、全局处理错误等
6. 接收响应：Axios 捕获服务端返回的响应，首先检查响应状态码，判断请求是否成功，根据配置自动将响应体从 JSON 格式转换为 JavaScript 对象

###### 拦截器

Axios 拦截器基于 JavaScript 的异步特性、Promise 链及拦截和修改请求或响应的机制，允许我们在请求发送前后及响应到达前后进行自定义逻辑。拦截器实质上可看作为 Promise 链中的一个中间件，请求拦截器在链的前端添加，响应拦截器在链的后端添加，这使得请求响应可通过一系列拦截器依照顺序进行处理即链式调用。Axios 内部维护两个拦截器列表，分别用于请求拦截器和响应拦截器，在拦截器链中，若任一拦截器抛出错误或返回一个 rejected 的 Promise，后续拦截器将不会执行，错误被直接传递到调用者的 catch 方法中

```js
let activeRequest = 0;
const toggleLoading = (isLoading) =>
  isLoading ? console.log("loading") : console.log("hide loading");
  
axios.interceptors.request.use(
  (config) => {
    ++activeRequest;
    if (activeRequest === 1) toggleLoading(true);
    return config;
  },
  (error) => Promise.reject(error)
);

axios.interceptors.response.use(
  (response) => {
    --activeRequest;
    if (!activeRequest) toggleLoading(false);
    return response;
  },
  (error) => {
    --activeRequest;
    if (!activeRequest) toggleLoading(false);
    return Promise.reject(error);
  }
);
```

###### 取消功能

Axios 的取消功能通过取消令牌机制实现，其允许在发送请求后主动取消请求。`CancelToken` 是用于取消请求的构造函数，它生成取消令牌，该令牌被绑定到一或多个请求上，当我们想取消某个请求时，使用该令牌发出取消信号，`axios.CancelToken.source` 返回一个包含 `token` 和 `cancel` 函数对象，而通过构造函数 new `axios.CancelToken` 创建一个 cancel token，传递一个执行函数给构造函数，该执行函数接收一个取消函数作为参数，在需要时调用该取消函数。`Cancel` 是在取消请求时抛出的特定错误对象，当取消请求时，Axios 抛出一个 `Cancel` 类型异常，我们通过捕获该异常来处理请求取消的情况

- 请求绑定：在发送请求时，若配置 `CancelToken`，该请求关联一个取消令牌，Axios 内部检查请求配置是否包含取消令牌，若存在，在发送请求前后添加相关的取消逻辑
- 监听取消事件：取消令牌内部维护一个 promise，该 promise 在令牌创建时是 pending 的，当调用取消令牌上的 `cancel` 方法时，该 promise 被 resolved，Axios 通过监听该 promise 的状态变化实现取消功能
- 执行取消操作：Axios 在底层根据运行环境不同使用不同 API 中断请求，若请求已发送，Axios 在浏览器中使用 XMLHttpRequest 的 `abort` 方法来取消请求，而在 Node.js 中直接关闭底层的 HTTP 请求
- 抛出取消异常：当取消请求时，Axios 抛出一个 `Cancel` 类型异常，我们通过捕获该异常来处理请求取消的情况

###### 在浏览器和 Node.js 中发送请求

Axios 能在浏览器和 Node.js 中发送请求在于其设计上的适配层和环境检测逻辑

1. 环境检测：Axios 内部进行环境检测，通过检查全局对象如 `window` 或 `global` 的存在性来判断代码是在浏览器还是 Node.js 环境中执行，根据环境检测结果，Axios 使用不同适配器来处理 HTTP 请求
2. 请求适配器：Axios 基于适配器模式设计，适配器为一种特殊的模块或函数，负责在特定环境下发送 HTTP 请求。Axios 内部实现至少两种适配器：

- 浏览器适配器：Axios 在浏览器中使用 XMLHttpRequest 对象发送请求
- Node.js 适配器：Axios 在 Node.js 中使用 Node.js 的核心模块如 `http` 或 `https` 发送请求

3. 自动切换逻辑：在初始化 Axios 时，其根据环境检测结果使用不同适配器来处理 HTTP 请求，我们无需关心当前代码是在哪个环境下执行，无需手动指定使用哪种方式来发送请求
4. 抽象封装：Axios 抽象封装请求和响应处理机制，确保在不同环境下有相对一致的 API

###### AJAX

[Ajax 知识体系大梳理 - 掘金](https://juejin.cn/post/6844903469896171533?searchId=202404230806386C14AAC7589AA688075C#heading-56)

Asynchronous JavaScript and XML，标准浏览器通过 `XMLHttpRequest`  对象实现 AJAX 的功能，IE 浏览器通过  `XMLHttpRequest`  或 `ActiveXObject`  对象同样实现 AJAX 的功能，XMLHttpRequest 实例对象没有自有属性，其所有属性来自于 `XMLHttpRequest.prototype`，`XMLHttpRequest` -> `XMLHttpRequest.prototype` -> `XMLHttpRequestEventTarget.prototype` -> `EventTarget.prototype` -> `Object.prototype`

浏览器线程机制：GUI 渲染线程 + JS 引擎线程 + 浏览器事件触发线程 + HTTP 请求线程

线程间交互以事件的形式触发, 通过事件回调予以通知，而事件回调, 又是以先进先出的方式添加到任务队列的末尾 , 直到 JS 引擎空闲时, 任务队列中的任务依次执行，即事件循环。对于一个 AJAX 请求, JS 引擎首先生成 `XMLHttpRequest` 实例对象, `open` 后调用 `send` 方法，它们都是同步执行的，但从 `send` 方法内部开始, 浏览器为即将发生的网络请求创建新的 HTTP 请求线程，其独立于 JS 引擎线程, 于是网络请求被异步发送，而 JS 引擎不会等待 AJAX 发送的 HTTP 请求接收到响应，它依照顺序继续执行任务队列中的任务。当 AJAX 请求被服务端响应且接收到 `response` 后, 浏览器事件触发线程捕获 AJAX 的事件回调 `onreadystatechange`，该事件回调并没有立即执行, 而是被添加到任务队列的末尾.，直到 JS 引擎空闲时，任务队列中的任务依次执行。`onreadystatechange` 事件内部操作 DOM，浏览器挂起 JS 引擎线程，执行 GUI 渲染线程，进行重绘回流，而后浏览器挂起 GUI 渲染线程，执行 JS 引擎线程，UI 渲染被加入到任务队列。JS 引擎线程和 GUI 渲染线程互斥，其他线程并行执行，AJAX 没有破坏 JS 的单线程机制

![[Pasted image 20240423083413.png]]
