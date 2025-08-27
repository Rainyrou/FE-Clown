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

1.  根据 `package.json` 中的 `scripts`  字段来执行 `npm run dev` ，Vite 读取项目根目录的配置文件 `vite.config.js`，合并默认配置和用户自定义配置如插件、服务器端口和代理等
2.  依赖预构建，Vite 扫描入口文件如  `index.html`，分析所有  `import`  语句，递归收集依赖列表，以 esbuild 打包 CommonJS/UMD 模块为 ES Module，并 Tree-Shaking 及缓存预构建结果，生成预构建的依赖文件默认置于 `node_modules/.vite` 目录，浏览器后续直接复用缓存而无需重复编译，生成 `metadata.json`，记录模块的依赖图谱和哈希值以验证缓存，浏览器通过 URL 中的查询哈希参数强缓存预构建的模块，若 `package.json`  或  `lockfile`  变化，Vite 自动重新预构建并更新哈希参数
3.  当用户访问页面时，Vite 返回 HTML 入口文件如 `index.html`，若 HTML 中存在 `<script type="module">`，Vite 自动注入 `/@vite/client` HMR 客户端脚本，服务端与客户端通据此建立 WebSocket 连接以传递热更新事件，浏览器解析 HTML 后，对入口文件发起网络请求，Vite dev server 拦截浏览器请求，实时按需编译并返回结果，调用对应插件将 Vue/React 组件转化为 CSS + JavaScript，将 CSS 文件转换为 JavaScript 文件并通过 `<style>` 标签动态注入，通过 esbuild 将 JavaScript/TypeScript 文件转换为浏览器兼容的 ES Module，而对于静态资源则直接返回解析后的 URL 
4. Vite 通过  `chokidar`  监听文件变化并标记为  `HMR Boundary`，当某模块更新时，更新事件沿着 ES Module 的 `import`  链向上查找最近的 `accept`  回调，Vite 基于 ES Module 的静态分析实现子模块级更新
5.  所有模块加载并执行完毕后，Vue/React 挂载根组件到 DOM 上，而后触发浏览器渲染
