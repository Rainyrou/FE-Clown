Webpack：

- 静态模块打包器，用于构建 JavaScript 应用程序，以一种相对一致且开放的处理方式加载所有资源文件并将其合并打包成浏览器兼容的 Web 资源，其忽略具体资源类型的差异，将所有文件统一视为 module，同一套代码实现诸多特性，以相同的加载、解析、依赖管理、优化和合并流程实现打包并借助 loader & plugin 开放接口将资源差异处理逻辑交由社区实现，建立统一资源构建模型，项目冷启动时 Webpack 递归打包整个项目的依赖树，在生产和开发阶段均打包，通过构建一至多个 bundle 文件来管理模块间的依赖关系，这样的全量编译方式及 JavaScript 语言本身的性能限制导致较慢的构建速度
- Webpack 解析配置文件 `webpack.config.js`，根据 Entry、Output、Loader、Plugin 和 Mode 等配置初始化，启用其内置在相应环境变量下的优化并实例化 `Compiler` 对象，构建时创建记录本次构建所有信息的 `Compilation` 对象，Webpack 从入口点开始解析文件，通过 AST 分析 import/require 语句，递归解析所有依赖项并构建包含所有模块及其依赖关系的依赖图，对于 CommonJS，Webpack 运行时注入的 webpack_require 模拟 CommonJS 模块加载机制处理循环依赖（模块缓存、部分执行等），返回已执行部分的 exports 对象；对于 ES Module，Webpack 通过静态分析处理循环依赖，Webpack 本身只支持 JavaScript，Loader 可将其他文件类型转换为有效的 module 并将其加入到依赖图中，而基于 Webpack 的事件钩子系统，Plugins 提供直接参与 Webpack 打包过程的机制，在构建的特定时刻添加自定义构建步骤，经过上述过程 Webpack 将源代码转换为最终的输出代码，其根据配置中的 `output` 字段将处理后的模块打包成一至多个 bundle 文件并输出到指定目录中，之后触发 `done` 事件，若构建过程中发生错误则显示错误信息甚至回滚到构建前的状态

Vite：

- no-bundle 服务 & 中间件机制：Vite 在开发阶段无需打包而基于浏览器天然支持的 ESM 的 no-bundle 服务加载模块，浏览器通过 `import`  语句按需请求模块，Vite dev server 首次启动时拦截浏览器请求，实时编译并返回结果，调用对应插件将 `import`  语句中的第三方依赖重写为预构建后的路径 `/node_modules/.vite/react.js`，将 Vue/React 组件转换为 JavaScript 和 CSS，将 CSS 转换为 JavaScript 并通过 `<style>` 标签动态注入，将 JavaScript/TypeScript 转换为 ES Module，而对于静态资源则返回解析后的 URL
- 依赖预构建：Vite 扫描入口文件，分析所有 `import`  语句，递归收集依赖列表，以 Esbuild 打包 CommonJS/UMD 为 ES Module 并缓存预构建结果，浏览器后续直接复用缓存，生成的 `metadata.json` 记录模块的依赖图谱和哈希值以验证缓存，浏览器通过 URL 的哈希参数强缓存预构建模块，若 `package.json`  或  `lockfile`  变化，自动重新预构建并更新哈希参数
- 热更新优化：Vite 自动注入 `/@vite/client` HMR 客户端脚本，服务端与客户端据此建立 WebSocket 连接以传递热更新事件，通过  `chokidar` 监听文件变化并标记为  `HMR Boundary`，当某模块更新时，更新事件沿着 ES Module 的 `import`  链向上查找最近的 `accept`  回调，对比 Webpack 的全量热更新方式，此种方式优化子模块级更新
- 集成 Rollup：Vite 在生产阶段基于 Rollup 打包，通过 ES Module 静态分析做 Tree-shaking 移除无用代码，支持动态 `import`  自动拆包，按需加载，以 Esbuild 实现 TypeScript 转换和语法降级

Loader：将其他格式的文件转换为 Webpack 支持打包的 module 即模块化非 JavaScript，通过转义以兼容旧版本浏览器及跨平台支持并减少构建产物打包体积，其本质为函数，支持同步、异步操作及链式调用，Webpack 检查 `import` 或 `require` 引入的非 JavaScript 文件配置中的 `module.rules` 并以相应 Loader 进行处理 ，Webpack 打包时按数组从右向左或从下到上的顺序将目标资源交由 Loader 处理

- babel-loader：基于 Babel，用于将 ES6+ 代码转换为目标浏览器支持的旧版本代码
- ts-loader：用于将 TypeScript 转换为 JavaScript
- style-loader：通过注入 `<style>` 标签将 CSS 添加到 DOM 中
- css-loader：用于解析 CSS 的 `@import` 和 `url` 函数等并将其转换为 JavaScript
- file-loader：用于处理其他格式的文件资源
- url-loader：用于处理图片，目标图片若大于指定大小则将其打包，否则将其转换为 base64 字符串并合并至 JavaScript 文件

Plugin：基于 Tapable 的 Webpack 事件钩子系统，在构建的各个阶段添加自定义的构建步骤。Webpack 的构建过程可视为生产线，从源文件经过多个处理阶段最终转换为输出结果，各个处理阶段职责单一且存在依赖关系，只有完成当前处理阶段，结果才能交由下一阶段继续处理，其保证构建的有序性，而 Plugin 可视为插入到该生产线中的功能模块，其通过监听 Webpack 的事件流机制在构建的特定阶段对资源进行定制化处理

- copy-webpack-plugin：用于将单文件或完整目录复制到构建目录
- html-webpack-plugin: 用于动态生成 HTML 文件并自动引入打包后生成的 bundle
- mini-css-extract-plugin: 用于从 JavaScript 文件中提取 CSS，生成独立于该 JavaScript 文件的 CSS 文件
- optimize-css-assets-plugin：压缩 CSS
- terser-webpack-plugin：压缩 JavaScript

自定义 Plugin：

- 编写 JavaScript 命名函数或类
- 在其原型上定义 `apply` 方法
- 指定挂载的 Webpack 事件钩子
- 处理 Webpack 内部实例的特定数据
- 功能完成后调用 Webpack 提供的回调

`import`：ES Module，其为 ES6 模块导入语法，在编译时执行（静态分析 Tree Shaking），支持动态导入模块，导入为原模块的动态绑定引用，循环引用时，未初始化的模块返回部分导出为空的对象

`require`：CommonJS，其为 Node.js 模块导入语法，在运行时执行（不支持静态分析），支持动态导入模块，导入为原模块的拷贝，循环引用时，未初始化的模块被访问时抛出 ReferenceError

```js
// a.js
export var a = 1;
export function modify() {
  a = 2;
}

// b.js
import { a, modify } from "./a.js";
console.log(a); // 1
modify();
console.log(a); // 2
```

ES Module 导入的为原模块的动态绑定引用，而非原模块的拷贝。`a.js` 中的变量 `a` 与 `b.js` 中导入的 `a` 指向同一内存地址，而 `modify` 函数修改 `a.js` 中的变量 `a`，因此所有导入 `a` 的地方均同步更新

ES Module：`import` 和 `export` 模块导入导出语法（浏览器 -> `<script type="module">`，Node.js -> `.mjs` 扩展名/在 `package.json` 设置 `"type": "module"`），在编译时执行（静态分析），同步异步加载模块
CommonJS：`require` 和 `module.exports` 模块导入导出语法（Node.js 原生支持），在运行时执行，同步加载模块

解决循环引用（循环依赖）：

- 定义：两个及以上模块相互引用形成闭环，本质为模块未初始化即相互引用（执行顺序的不确定性）
- Webpack：在编译阶段确定模块的依赖关系并构建模块依赖图，CommonJS 通过 webpack_require 模拟加载（模块缓存检测、部分执行等），返回已执行的 exports；ES Module 通过静态分析（缓存检测、实时绑定、静态提升等）
- Vite：在编译阶段确定模块的依赖关系并构建模块依赖图，以 Esbuild 打包 CommonJS/UMD 为 ES Module，通过静态分析（缓存检测、实时绑定、静态提升等）

Tree-Shaking：ES Module 通过分析模块导入导出，在编译阶段确定模块的依赖关系并构建模块依赖图，通过静态分析遍历所有模块并标记未引用模块，在构建阶段移除无用代码

动态导入的 Tree-Shaking 实现方式：ES Module + 无副作用代码标注 + 通过 `optimization.splitChunks/rollupOptions.manualChunks` 为各个模块生成独立 Chunk

代码分割：

- 入口分割：根据模块依赖图识别多页面入口点，为各个入口点生成独立 Chunk
- 动态导入：构建工具静态分析 `import` 语法，根据模块间的依赖关系生成独立 Chunk
- 共享模块：通过 `optimization.splitChunks/rollupOptions.manualChunks` 提取共享模块为公共 Chunk，配置"长缓存 + MD5 哈希"，后续直接复用缓存

依赖注入：组件声明依赖而无需关心实现细节，容器通过 Token-Provider 映射创建并缓存依赖实例，通过静态分析构建模块依赖图保证创建顺序，最终将实例注入组件

Workspace：

- 符号链接：A 包依赖 B 包，按传统发布流程需先发布 B 包至 npm，A 包通过 npm registry 加载 B 包，但 Workspace（Monorepo）直接在 A 包的 `node_modules` 目录下创建指向 B 包源代码的符号链接
- 依赖提升与扁平化：包管理器解析依赖树时执行依赖提升，检测所有子包中共享的、版本兼容的依赖，将其安装于项目根目录下扁平化的 `node_modules` 目录
- pnpm workspace 优化：在 npm/yarn 的扁平化结构中，某包可访问未在其 `package.json` 中声明但在根目录被 依赖提升的依赖，pnpm 维持严格的依赖关系树，在根目录通过硬链接将依赖存储于全局内容可寻址存储，再在各个包的 `node_modules` 中通过符号链接确保其只能访问其 `package.json` 中明确声明的依赖

npm & pnpm：

- 安装速度：npm 安装依赖比 pnpm 慢，复制完整依赖比复制硬链接和符号链接慢
- 存储策略：当通过 npm 于不同项目安装相同依赖时，其存储这些依赖于各个项目的 `node_modules` 中，而 pnpm 在根目录通过硬链接将依赖存储于全局内容可寻址存储，依赖无论安装几次，在硬盘上只存储一次，同时在 `node_modules` 中为各个包创建硬链接或符号链接，项目据此访问全局存储的依赖
- 依赖关系：在 npm/yarn 的扁平化结构中，某包可访问未在其 `package.json` 中声明但在根目录被依赖提升的依赖，pnpm 维持严格的依赖关系树，在根目录通过硬链接将依赖存储于全局内容可寻址存储，再在各个包的 `node_modules` 中通过符号链接确保其只能访问其 `package.json` 中明确声明的依赖
- Monorepo 支持 & 兼容性：pnpm 原生支持 Monorepo，兼容性不如 npm，命令参数和选项有所差异

Dev Dependency：在开发测试编译构建必须存在的依赖（测试/构建/格式化/类型定义工具），其不具有传递性；由工具链加载和执行如构建工具打包代码、Babel  编译 ES6 → ES5、Jest  单元测试，其作用在构建阶段结束后完成，不被打包至最终生产 Bundle

Dependency：在生产阶段运行时必须存在的依赖（框架/核心库/核心工具），其具有传递性，当 A 包依赖 B 包，而 B 包依赖 C 包时，C 包自动安装 ​ 并成为 A 包的传递依赖，出现于 A 包的 `node_modules` 中；JavaScript 运行时在项目部署并执行业务逻辑时，通过 `import` 或 `require` 语句加载解析 Dependency 依赖，其被打包至最终生产 Bundle

Module：分割程序所得到的独立片段即模块，各个模块执行特定任务且可被其他模块按需引用
Chunk：由多个 module 组成，代码分割即将代码分割为多个 Chunk，按需加载对应 Chunk
Bundle：构建过程的最终输出

通过配置 `module.rules`  匹配指定资源类型及 `url-loader`  和  `style-loader` 将静态资源转换为 Base64 或 CSS 字符串内联至同一 JavaScript Bundle 中
