Node 模块：

- util：提供工具如继承方法和检查数据类型等
- fs：文件操作如读写等
- path：处理和转换文件路径等
- os：提供操作系统相关功能
- http：提供 HTTP 服务端和客户端功能
- url：解析和创建 URL
- buffer：处理二进制
- stream：处理数据流
- crypto：加密解密
- events：创建、触发和监听自定义事件

EventEmitter：发布 - 订阅模式在 Node.js 的原生实现，Node.js 中间件通过 EventEmitter 实现请求异步机制，为各个请求创建独立的 EventEmitter 实例，绑定请求相关的异步事件，通过事件触发 - 监听解耦异步逻辑，适配中间件的请求生命周期
