wx 小程序是一种无需下载安装即可开箱即用的应用，它实现了应用程序触手可及 & 用完即走的理念，用户扫码 or 搜索即可打开，无需关心安装问题

###### 1. 运行环境

wx 小程序运行在 wx 内的一个轻量级、高效且安全的环境中。它由 View 和 App Service 组成，此二者运行于两个不同的线程中

- 视图层：WXML、WXSS 渲染用户界面
- 逻辑层：JavaScript 处理业务逻辑

###### 2. 双线程模型

- 渲染线程：在 WebView 中运行，负责渲染用户界面
- 逻辑线程：在 JSCore 中运行，负责执行 JavaScript 代码来处理业务逻辑

这两个线程通过 wx Native 桥来进行通信

###### 3. 通信机制

- 视图层和逻辑层间的通信是异步的，通过原生 `setData` 方法进行数据传输
- 当数据在逻辑层变化时，它们被序列化后发送给视图层，在视图层更新
- 视图层通过事件处理机制向逻辑层发送用户交互信息

###### 4. 生命周期 & 原生组件、API

###### 5. 性能优化

- 支持分包加载 & 懒加载

###### 6. 安全性

- 小程序运行在 wx 的沙箱环境，它们没有访问手机本地文件等权限，保障了安全性
- wx 团队对小程序内容进行严格审核，保证内容质量


