App Service 逻辑层与 WebView 渲染层分离，通过 `Native` 层桥接通信，数据传递通过 `evaluateJavascript` 实现，原生组件直接由 Native 渲染，与 WebView 中的组件混合渲染时的层级问题，通过 `subpackages` 配置分包加载（主包 ≤ 2MB，总包 ≤ 20MB），代码注入优化，减少 `App.onLaunch` 同步逻辑，异步非关键任务，避免频繁 `setData`，合并数据更新，使用 `WXS` 处理轻量级逻辑，长列表使用 `recycle-view` 或 `virtual-list` 复用节点，敏感接口在 `app.json` 中声明

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