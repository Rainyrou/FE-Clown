Webpack：静态模块打包器，用于构建 JavaScript 应用程序，以一种相对一致且开放的处理方式加载所有资源文件并将其合并打包成浏览器兼容的 Web 资源，其忽略具体资源类型的差异，将所有文件统一视为 module，同一套代码实现诸多特性，以相同的加载、解析、依赖管理、优化和合并流程实现打包并借助 loader & plugin 开放接口将资源差异处理逻辑交由社区实现，建立统一资源构建模型，项目冷启动时 Webpack 递归打包整个项目的依赖树，在生产和开发阶段均打包，通过构建一至多个 bundle 文件来管理模块间的依赖关系，这样的全量编译方式及 JavaScript 语言本身的性能限制导致较慢的构建速度

Webpack 解析配置文件 `webpack.config.js`，根据 Entry、Output、Loader、Plugin 和 Mode 等配置初始化，启用其内置在相应环境变量下的优化并实例化 `Compiler` 对象，构建时创建记录本次构建所有信息的 `Compilation` 对象，Webpack 从入口点开始解析文件，其为 Webpack 查找所有依赖即 import/require 语句的起点，接着递归解析所有依赖项并构建依赖图，依赖图包含项目所有模块并记录它们间的依赖关系，Webpack 本身只支持 JavaScript，Loader 可将其他文件类型转换为有效的 module 并将其加入到依赖图中，而基于 Webpack 的事件钩子系统，Plugins 提供一种机制直接参与到 Webpack 的打包过程，允许在构建的特定时刻添加自定义构建步骤，经过上述过程 Webpack 将源代码转换为最终的输出代码，其根据配置中的 `output` 字段将处理后的模块打包成一至多个 bundle 文件并输出到指定目录中，之后触发 `done` 事件，若构建过程中发生错误则显示错误信息甚至回滚到构建前的状态

Vite

1. no-bundle 服务 & 中间件机制：Vite 在开发阶段无需打包而基于浏览器天然支持的 ESM 的 no-bundle 服务加载模块，浏览器通过 `import`  语句按需请求模块，Vite dev server 首次启动时拦截浏览器请求，实时编译并返回结果，调用对应插件将 `import`  语句中的第三方依赖重写为预构建后的路径 `/node_modules/.vite/react.js`，将 Vue/React 组件转换为 JavaScript 和 CSS，将 CSS 转换为 JavaScript 并通过 `<style>` 标签动态注入，将 JavaScript/TypeScript 转换为 ES Module，而对于静态资源则返回解析后的 URL
2. 依赖预构建：Vite 扫描入口文件，分析所有 `import`  语句，递归收集依赖列表，以 Esbuild 打包 CommonJS/UMD 为 ES Module 并缓存预构建结果，浏览器后续直接复用缓存，生成的 `metadata.json` 记录模块的依赖图谱和哈希值以验证缓存，浏览器通过 URL 的哈希参数强缓存预构建模块，若 `package.json`  或  `lockfile`  变化，自动重新预构建并更新哈希参数
3. 热更新优化：Vite 自动注入 `/@vite/client` HMR 客户端脚本，服务端与客户端据此建立 WebSocket 连接以传递热更新事件，通过  `chokidar` 监听文件变化并标记为  `HMR Boundary`，当某模块更新时，更新事件沿着 ES Module 的 `import`  链向上查找最近的 `accept`  回调，对比 Webpack 的全量热更新方式，此种方式优化子模块级更新
4. 集成 Rollup：Vite 在生产阶段基于 Rollup 打包，通过 ES Module 静态分析做 Tree-shaking 移除无用代码，支持动态 `import`  自动拆包，按需加载，以 Esbuild 实现 TypeScript 转换和语法降级
