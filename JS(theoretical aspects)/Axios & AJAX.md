Axios：基于 Promise，其作为函数/对象，当作为函数调用时，实际调用 `Axios.prototype.request` 方法，当作为对象使用时，其封装 `request` 方法并预设请求方法类型，Axios 初始化时自动创建实例（可通过 `axios.create(config)` 手动创建），其配置默认请求参数，合并配置对象，在请求发送前，其遍历并执行注册的所有请求拦截器，请求拦截器处理请求数据，而在响应返回给调用者前，其逆序遍历并执行注册的所有响应拦截器，响应拦截器处理响应数据

Axios 拦截器（Promise 链中间件）基于 JavaScript 异步特性、Promise 链及拦截和修改请求响应的机制，在请求发送/响应到达前后自定义逻辑，请求拦截器在Promise 链前端添加，响应拦截器在Promise 链后端添加以链式调用，其内部维护两个拦截器列表分别用于请求和响应拦截器，在拦截器链中若任一拦截器抛出错误或返回 rejected Promise，后续拦截器不再执行，通过 catch 捕获错误

Asynchronous JavaScript and XML，标准浏览器通过 `XMLHttpRequest`  对象实现 AJAX 功能，IE 浏览器通过  `XMLHttpRequest`  或 `ActiveXObject`  对象实现 AJAX 功能，XMLHttpRequest 实例对象无自有属性，其所有属性源于 `XMLHttpRequest.prototype`，`XMLHttpRequest` -> `XMLHttpRequest.prototype` -> `XMLHttpRequestEventTarget.prototype` -> `EventTarget.prototype` -> `Object.prototype`

浏览器线程机制：GUI 渲染线程 + JavaScript 引擎线程 + 事件触发线程 + HTTP 请求线程

线程间以事件形式触发交互，通过事件回调予以通知，事件回调以先进先出的方式添加至任务队列末尾，直到 JavaScript 引擎线程空闲时，依次执行任务队列的任务，即事件循环。对于 AJAX 请求，JavaScript 引擎线程生成 `XMLHttpRequest` 实例对象，调用 `send` 方法，浏览器为请求创建新的异步 HTTP 请求线程，JavaScript 引擎继续依次执行任务队列的任务，当 AJAX 请求接收到服务端响应后，事件触发线程捕获 AJAX 事件回调 `onreadystatechange`，其被添加至任务队列末尾。当JavaScript 引擎线程空闲时，`onreadystatechange` 事件内部操作 DOM

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




