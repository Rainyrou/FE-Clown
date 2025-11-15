`React.lazy` 接收返回 Promise 的函数为参数并将其包装为惰性组件，React 渲染该组件时调用动态导入函数，浏览器发送请求获取该组件对应 Chunk， `React.lazy` 内部维护该 Promise 的状态，不支持 SSR，因为服务端无浏览器的动态导入机制，且 React.lazy 异步加载为运行时行为与 SSR 预编译所有组件的逻辑冲突，需通过将运行时动态导入转换为打包时预编译异步组件元信息 + 服务端同步加载组件以兼容此类场景

- Promise 状态为 pending，向父级上下文抛出对应 Promise
- Promise 状态为 resolved，渲染组件
- Promise 状态为 rejected，通过 Error Boundary 捕获错误

Suspense：监听所有子组件异步资源的加载状态并捕获其抛出的 pending Promise，渲染 `fallback`  属性指定的 UI，待所有 Promise resolved 后重新渲染子组件

React Suspense、React.lazy 与路由实现代码分割：各个路由对应页面级功能模块，通过 `React.lazy` 包装页面组件，构建工具为各个页面组件生成独立 Chunk，应用启动时加载主 Chunk和当前路由匹配的Chunk，切换路由时按需加载对应页面组件
