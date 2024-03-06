##### Webpack

Webpack HMR 可以在不刷新整个页面的情况下增删、修改模块

Webpack HMR 的配置包括：

- 在 Webpack 配置文件中启用 `HotModuleReplacementPlugin`
- 在 Webpack Dev Server 配置中开启 HMR
- 在应用代码中添加 HMR 接口，如使用 `module.hot.accept` 来指定模块热更新逻辑

###### 底层原理

1. 当使用 Webpack dev server 等工具时，Webpack 会监控文件的变化。一旦变化，Webpack 就会重新编译并生成新的模块
2. 修改后的模块通过 WebSocket 发送到浏览器
3. 在接收到新的模块后，浏览器的 HMR 插件根据需要替换旧模块。这是通过 HMR API 实现的，你可以在代码中指定模块的更新方式
4. 对于 CSS/SCSS 等样式文件或 Vue/React 组件，HMR 可以直接替换旧文件，无需重新加载整个页面

###### Vite

Vite dev server 是一个基于 ESM 的开发服务器，它利用浏览器原生 ESM 导入功能实现 HRM。Vite HRM 无需额外配置，因为它基于原生 ESM 和服务器推送。在大多数情况下，Vite 自动处理 HRM

###### 底层原理

1. Vite 基于原生 ESM 和服务器推送，当文件变化时，Vite 通知浏览器获取修改后的模块
2. Vite dev server 会监控文件的变化，并通过 WebSocket 向浏览器推送更新
3. 浏览器会根据 Vite 的更新信息，仅重新请求修改后的模块，实现快速替换
4. Vite 在开发环境中不需要打包操作，它直接提供源文件给浏览器，这加快了 HRM 速度
