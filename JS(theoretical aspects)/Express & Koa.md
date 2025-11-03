Express：Node.js 框架，中间件以线性方式组织，接收 `res`、`req` 和 `next` 参数，通过 `next` 和回调调用下一中间件，或不调用 `next` 中断执行，若中间件抛出错误，调用 `next(error)` 跳过后续所有非错误处理的中间件，直接将控制权传递给 `(err, req, res, next)` 四参数的错误处理中间件（位于中间件栈的最底部）

1. express.Router：用于定义路由的方式，使用基于路径和 HTTP 方法的路由定义方式，将特定路由映射到一至多个处理函数上，在底层上使用路由表来存储和管理这些路径和 HTTP 方法与处理函数的映射关系，当接收到请求时，Express 根据映射关系查找匹配的处理函数执行，提供 `get`、`post`、`put` 和 `delete` 等方法来处理不同类型的请求，每个路由可能关联多个中间件，允许复杂的路由处理逻辑
2. body-parser：用于解析 HTTP 请求体并将解析后的数据添加到 `req.body` 中
3. express.static：用于托管如 HTML、CSS、JavaScript 等静态文件，基于请求的 URL 来提供对应的文件内容，若请求的 URL 匹配文件系统中的静态文件，返回该文件给客户端，否则将控制权传递给下一中间件
4. express-handlebars：用于模板渲染，支持模板引擎如 Handlebars 等，提供 `res.render` 方法，用于在中间件中渲染视图并自动将渲染后的 HTML 发送给客户端
5. express-session：用于跟踪会话，使用 Cookie 存储会话标识并将会话存储于服务端。当接收请求时，根据 Cookie 中的会话标识查找对应的会话数据，加载到 `req.session` 中

Koa：由 Express 原班人马开发，比 Express 轻量，Koa 1.0 与 Koa 2.0 的区别在于其对中间件的处理和异步编程的支持。Koa 1.0 使用基于 Generator 的中间件，这些中间件依赖于 `yield` 来暂停和恢复执行，配合 `co` 库来自动执行 Generator。Koa 2.0 的中间件完全支持 Promise，使用 `async/await` 和 `compose` 函数来避免回调地狱，使用 `try/catch` 处理错误。虽然 Koa 1.0 与 Koa 2.0 底层的 HTTP 处理逻辑相同，但 Koa 2.0 有更好的性能，因为其直接利用 V8 引擎对 `async/await` 的优化。通过 `new Koa` 创建 Koa 实例，Koa 核心为一个中间件数组，通过 `app.use` 注册的中间件被添加到该数组中，当接收到 HTTP 请求时，Koa 为每个请求创建一个上下文对象 `ctx`，`ctx` 封装 Node.js 的原生 `req` 和 `res` 对象，包含请求和响应的状态信息并提供一系列用于操作请求响应的方法。通过 `app.listen` 创建并监听 HTTP 服务器，在底层上调用 Node.js 的 `http.createServer` 方法并将 Koa 应用实例作为请求处理函数，而该请求处理函数实际上是通过调用 `app.callback` 获取的

洋葱模型：处理 HTTP 请求时，中间件的执行顺序和嵌套结构类似于洋葱层次，Koa 中间件执行机制依赖于 `koa-compose` 模块，其允许中间件以堆叠的方式执行，其中每个中间件在请求继续处理前后执行代码。当一个请求进入 Koa 应用时，它从第一个注册的中间件开始执行，直到某个中间件不调用 `await next` 或中间件堆栈结束，`next` 用于暂停当前中间件的执行并将执行权传递给下一中间件，当下游的中间件全部执行完毕，控制权返回给当前的中间件，继续执行 `await next` 后的代码

```js
function compose(arr) {
  return function (ctx) {
    function next() {
      if (arr.length) {
        let func = arr.shift();
        func(ctx, next);
      }
    }
    if (arr.length) {
      let func = arr.shift();
      func(ctx, next);
    }
  };
}
```

1. koa-router：用于定义路由方式，它维护一个路由表，支持 RESTful 风格的路由并提供 `get`、`post`、`put` 和 `delete` 等方法来处理不同类型的请求，每个路由可能关联多个中间件，允许复杂的路由处理逻辑
2. koa-bodyparser：用于解析 HTTP 请求体并将解析后的数据添加到 `ctx.request.body` 中
3. koa-static：用于托管如 HTML、CSS、JavaScript 等静态文件，基于请求的 URL 来提供对应的文件内容，若请求的 URL 匹配文件系统中的静态文件，返回该文件给客户端，否则将控制权传递给下一中间件
4. koa-views：用于模板渲染，支持多种模板引擎如 Pug、Mustache 等，提供 `ctx.render` 方法，用于在中间件中渲染视图并自动将渲染后的 HTML 发送给客户端
5. koa-session：用于跟踪会话，使用 Cookie 存储会话标识并将会话存储于服务端。当接收请求时，根据 Cookie 中的会话标识查找对应的会话数据，加载到 `ctx.session` 中

错误处理：

1. 中间件通过 `async/await` 和 `try/catch` 捕获错误
2. 全局错误事件监听：中间件抛出错误未被捕获，Koa 自动触发 `error` 事件，通过全局错误事件监听处理错误

```js
app.on('error', (err, ctx) => console.error('server error', err, ctx));
```

3. 异常中间件：类似 Express 的错误处理中间件，在 Koa 实现用于处理错误的中间件
