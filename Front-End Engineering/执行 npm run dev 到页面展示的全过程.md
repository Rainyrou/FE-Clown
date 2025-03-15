```js
npm run dev
  → 读取配置
  → 依赖预构建
  → 启动开发服务器 + 文件监听
  → 浏览器请求 HTML + 注入 HMR 客户端
  → 拦截请求 + 按需编译文件 + 处理依赖引用
  → 动态加载模块 + 增量更新
  → 框架初始化 + 页面渲染
```

1.  通过 `package.json` 中的 `scripts`  字段，执行 `npm run dev` 命令，Vite 读取项目根目录的  `vite.config.js`，合并默认配置和用户自定义配置如插件、服务器端口和代理等
2.  依赖预构建，通过 ESBuild  打包 CommonJS/UMD 模块为 ES Module，生成预构建的依赖文件并存储于  `node_modules/.vite` 目录，浏览器后续直接加载预构建后的依赖，避免重复编译
3.  启动基于 Koa 的轻量级开发服务器，其负责处理文件请求、代码转换和热更新
4.  当用户访问页面时，Vite 返回 HTML 入口文件如 `index.html`，若 HTML 中存在 `<script type="module">`，Vite 自动注入 `/@vite/client` HMR 客户端脚本以建立 WebSocket 连接和服务端通信，浏览器解析 HTML 后，对入口文件发起请求，Vite 拦截请求，按需编译文件，调用对应插件将 Vue/React 组件转化为 CSS + JS，将 CSS 文件转换为 JS 文件并通过 `<style>` 标签动态注入，通过 ESBuild 将 JS/TS 文件转换为浏览器兼容的 ES 模块，而对于静态资源则直接返回解析后的 URL，将  `import` 语句中的第三方依赖重写为预构建后的路径 
5. Vite 通过文件监听系统捕获文件的修改，计算变更影响的范围，仅重新编译受影响模块即增量更新，通过 WebSocket 通知浏览器更新模块而非刷新页面，HMR 客户端根据服务端指令，动态替换旧模块并执行相关生命周期钩子如 Vue 组件的 `hot.accept`
6.  所有模块加载并执行完毕后，Vue/React 挂载根组件到 DOM，之后是浏览器渲染过程
