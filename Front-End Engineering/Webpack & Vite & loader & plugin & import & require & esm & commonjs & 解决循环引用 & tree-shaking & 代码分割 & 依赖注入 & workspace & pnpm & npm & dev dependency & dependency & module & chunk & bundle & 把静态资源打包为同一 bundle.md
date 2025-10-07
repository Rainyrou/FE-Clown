参考文章：[美团点餐 - Webpack 之 Loader 和 Plugin 简介](https://juejin.cn/post/6844903489458405390?searchId=20241117171358EFAC48295E488CE488B5)

Webpack：静态模块打包器，用于构建 JavaScript 应用程序，以一种相对一致且开放的处理方式加载所有资源文件并将其合并打包成浏览器兼容的 Web 资源，其忽略具体资源类型的差异，将所有文件统一视为 module，同一套代码实现诸多特性，以相同的加载、解析、依赖管理、优化和合并流程实现打包并借助 loader & plugin 开放接口将资源差异处理逻辑交由社区实现，建立统一资源构建模型，项目冷启动时 Webpack 递归打包整个项目的依赖树，在生产和开发阶段均打包，通过构建一至多个 bundle 文件来管理模块间的依赖关系，这样的全量编译方式及 JavaScript 语言本身的性能限制导致较慢的构建速度

Webpack 解析配置文件 `webpack.config.js`，根据 Entry、Output、Loader、Plugin 和 Mode 等配置初始化，启用其内置在相应环境变量下的优化并实例化 `Compiler` 对象，构建时创建记录本次构建所有信息的 `Compilation` 对象，Webpack 从入口点开始解析文件，通过 AST 分析 import/require 语句，递归解析所有依赖项并构建包含所有模块及其依赖关系的依赖图，对于 CommonJS，Webpack 运行时注入的 webpack_require 模拟 CommonJS 模块加载机制处理循环依赖（模块缓存、部分执行等），返回已执行部分的 exports 对象；对于 ES Module，Webpack 通过静态分析处理循环依赖，Webpack 本身只支持 JavaScript，Loader 可将其他文件类型转换为有效的 module 并将其加入到依赖图中，而基于 Webpack 的事件钩子系统，Plugins 提供直接参与 Webpack 打包过程的机制，在构建的特定时刻添加自定义构建步骤，经过上述过程 Webpack 将源代码转换为最终的输出代码，其根据配置中的 `output` 字段将处理后的模块打包成一至多个 bundle 文件并输出到指定目录中，之后触发 `done` 事件，若构建过程中发生错误则显示错误信息甚至回滚到构建前的状态

Vite：

1. no-bundle 服务 & 中间件机制：Vite 在开发阶段无需打包而基于浏览器天然支持的 ESM 的 no-bundle 服务加载模块，浏览器通过 `import`  语句按需请求模块，Vite dev server 首次启动时拦截浏览器请求，实时编译并返回结果，调用对应插件将 `import`  语句中的第三方依赖重写为预构建后的路径 `/node_modules/.vite/react.js`，将 Vue/React 组件转换为 JavaScript 和 CSS，将 CSS 转换为 JavaScript 并通过 `<style>` 标签动态注入，将 JavaScript/TypeScript 转换为 ES Module，而对于静态资源则返回解析后的 URL
2. 依赖预构建：Vite 扫描入口文件，分析所有 `import`  语句，递归收集依赖列表，以 Esbuild 打包 CommonJS/UMD 为 ES Module 并缓存预构建结果，浏览器后续直接复用缓存，生成的 `metadata.json` 记录模块的依赖图谱和哈希值以验证缓存，浏览器通过 URL 的哈希参数强缓存预构建模块，若 `package.json`  或  `lockfile`  变化，自动重新预构建并更新哈希参数
3. 热更新优化：Vite 自动注入 `/@vite/client` HMR 客户端脚本，服务端与客户端据此建立 WebSocket 连接以传递热更新事件，通过  `chokidar` 监听文件变化并标记为  `HMR Boundary`，当某模块更新时，更新事件沿着 ES Module 的 `import`  链向上查找最近的 `accept`  回调，对比 Webpack 的全量热更新方式，此种方式优化子模块级更新
4. 集成 Rollup：Vite 在生产阶段基于 Rollup 打包，通过 ES Module 静态分析做 Tree-shaking 移除无用代码，支持动态 `import`  自动拆包，按需加载，以 Esbuild 实现 TypeScript 转换和语法降级

Loader：将其他格式的文件转换为 Webpack 支持打包的 module 即模块化非 JavaScript，通过转义以兼容旧版本浏览器及跨平台支持并减少构建产物打包体积，其本质为函数，支持同步、异步操作及链式调用，Webpack 检查 `import` 或 `require` 引入的非 JavaScript 文件配置中的 `module.rules` 并以相应 Loader 进行处理 ，Webpack 打包时按数组从右向左或从下到上的顺序将目标资源交由 Loader 处理

```
output=loader(input) // input 为源文件字符串/上一 loader 转换后的结果
```

```js
const loaderUtils = require("loader-utils");

// source：首个执行的 loader 之资源文件内容
// sourceMap: 代码 SourceMap 结构
// data: 其它元信息
module.exports = function (source, sourceMap?, data?) {
  const options = loaderUtils.getOptions(this);
  return source;
};
```

1. babel-loader：基于 Babel，用于将 ES6+ 代码转换为目标浏览器支持的旧版本代码
2. ts-loader：用于将 TypeScript 转换为 JavaScript
3. style-loader：通过注入 `<style>` 标签将 CSS 添加到 DOM 中
4. css-loader：用于解析 CSS 的 `@import` 和 `url` 函数等并将其转换为 JavaScript
5. file-loader：用于处理其他格式的文件资源
6. url-loader：用于处理图片，目标图片若大于指定大小则将其打包，否则将其转换为 base64 字符串并合并至 JavaScript 文件

Loader 的使用：

1. 在 `webpack.config.js` 中配置
2. 通过内联使用，在 `import` 语句中显示指定

自定义 Loader：

`my-loader.js`

```js
module.exports = function (content) {
  this.cacheable && this.cacheable(); // 启用 Loader 缓存
  this.value = content; // 保存传入内容，便于链式调用
  return "module.exports = " + JSON.stringify(content); // 将内容转换为 JavaScript 模块导出形式
};
```

`webpack.config.js`

```js
const path = require("path");

module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.txt$/,
        use: path.resolve(__dirname, "my-loader.js"),
      },
    ],
  },
};
```

在 `src` 文件夹下创建一个测试文件 `example.txt`：

```txt
Hello, Webpack Loader!
```

在 `src/index.js` 中引入：

```js
import text from './example.txt';
console.log(text);
```

在命令行执行：

```bash
npx webpack
```

输出 `dist/bundle.js` 文件：

```js
module.exports = "Hello, Webpack Loader!";
```

自定义 Loader 原则：

- Loader 生成的模块与 Webpack 设计一致，遵循 JavaScript 模块规范 ES Modules 或 CommonJS，支持模块化加载
- 将通用逻辑抽离为独立模块，避免重复生成
- Loader 专注于单一任务，避免复杂多任务逻辑
- Loader 不应保存任何与模块编译相关的状态，每次调用均独立完成任务，若需共享状态，可通过 Webpack 提供的缓存机制
- 每个 Loader 均应尽可能输出适合于下一 Loader 处理的内容，通过多个 Loader 链式调用实现复杂功能
- 通过 `this.addDependency` 手动声明外部依赖，`this.resolve` 解析模块依赖，将可能引发版本冲突的依赖声明为 `peerDependencies`
- 通过 `loader-utils` 和 `schema-utils` 提供的工具处理 Loader 选项和验证，使用 `loader-utils` 提供的 `stringifyRequest` 将绝对路径转换为相对路径

Plugin：基于 Tapable 的 Webpack 事件钩子系统，在构建的各个阶段添加自定义的构建步骤。Webpack 的构建过程可视为生产线，从源文件经过多个处理阶段最终转换为输出结果，各个处理阶段职责单一且存在依赖关系，只有完成当前处理阶段，结果才能交由下一阶段继续处理，其保证构建的有序性，而 Plugin 可视为插入到该生产线中的功能模块，其通过监听 Webpack 的事件流机制在构建的特定阶段对资源进行定制化处理

1. copy-webpack-plugin：用于将单文件或完整目录复制到构建目录
2. html-webpack-plugin: 用于动态生成 HTML 文件并自动引入打包后生成的 bundle
3. mini-css-extract-plugin: 用于从 JavaScript 文件中提取 CSS，生成独立于该 JavaScript 文件的 CSS 文件
4. optimize-css-assets-plugin：压缩 CSS
5. terser-webpack-plugin：压缩 JavaScript

自定义 Plugin：

- 编写一个 JavaScript 命名函数或类
- 在其原型上定义一个 `apply` 方法
- 指定挂载的 Webpack 事件钩子
- 处理 Webpack 内部实例的特定数据
- 功能完成后调用 Webpack 提供的回调

```js
class FileListPlugin {
  constructor(options) {
    this.options = options || {};
  }
  apply(compiler) {
    // 在 "emit" 阶段挂载钩子
    compiler.hooks.emit.tapAsync("FileListPlugin", (compilation, callback) => {
      let filelist = "In this build:\n\n"; // 创建文件列表
      for (const filename in compilation.assets) filelist += `- ${filename}\n`;
      // 将 filelist.md 添加到构建的输出中
      compilation.assets["filelist.md"] = {
        source: () => filelist,
        size: () => filelist.length,
      };
      callback();
    });
  }
}

module.exports = FileListPlugin;
```

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

解决循环引用：

循环引用（循环依赖）：两个及以上模块相互引用，形成闭环。直接循环依赖如模块 A 引入模块 B，模块 B 引入模块 A；间接循环依赖链路更长如模块 A 引入模块 B，模块 B 引入模块 C，模块 C 引入模块 A​，无论哪种表现形式，本质为模块间相互引用导致未初始化即被使用（执行顺序不确定性，先有鸡还是先有蛋）

Webpack 从入口点开始解析文件，通过 AST import/require 语句，递归解析所有依赖项并构建包含所有模块及其依赖关系的依赖图，对于 CommonJS，Webpack 运行时注入的 webpack_require 模拟 CommonJS 模块加载机制处理循环依赖（模块缓存检测、部分执行等），返回已执行部分的 exports 对象；对于 ES Module，Webpack 通过静态分析处理循环依赖（模块缓存检测、实时绑定、静态提升等）

Vite 基于原生支持和最小化干预的策略，扫描入口文件，分析所有 `import` 语句，递归收集依赖列表，以 Esbuild 打包 CommonJS/UMD 为 ES Module（模块缓存检测、实时绑定、静态提升等）

Tree-Shaking：ES Module 的依赖关系在编译时是确定的，支持静态分析即在不执行代码的情况下分析模块的导入导出以判断哪些部分是可达的，哪些部分是死代码（无法到达 -> 在某些条件下如 `return` 语句后永远无法执行的代码 + 无用计算结果如未被使用的函数返回值 + 只写不读的变量），满足 Tree-Shaking 的条件，通过解析模块的导入导出，识别模块间的依赖关系并构建模块依赖图，遍历所有模块并以 `usedExports` 标记未被引用的模块导出，在构建时移除未被使用的 JavaScript 代码，减少构建产物大小

局限性：

- 无法分析动态导入和 CommonJS 的依赖关系
- 无法移除有副作用的模块

代码分割：通过静态分析获取模块间的依赖关系，哪些模块是可拆分的，哪些模块是相互依赖的，哪些模块是共享的，哪些模块是首屏所需的，根据模块依赖图采取以下策略：

- 通过入口分割拆分不同页面代码，为各个入口点生成独立 Chunk，各个 Chunk 仅包含该入口点所需代码
- 通过动态 `import` 按需加载，当运行时代码执行至动态 `import` 部分时，Webpack 或 Vite 据此生成并加载独立 Chunk
- 通过 Webpack `optimization.splitChunks` 和 Vite `rollupOptions.manualChunks` 提取多模块间共享部分为独立 Chunk，避免重复加载相同模块

依赖注入：实现控制反转，组件只需声明依赖，而无需关心依赖实现细节，而容器提供依赖，记录"依赖标识（Token）→ 依赖创建方式（Provider）"的映射关系，通过分析获取模块间的依赖关系，构建模块依赖图并拓扑排序，通过 Provider 创建实例，根据生命周期规则（单例/多例）缓存依赖实例，容器通过 Token 查找解析后的依赖实例，再通过组件暴露的注入点（属性、构造函数参数、函数参数、配置声明等）传递实例至组件中


Workspace：

- 符号链接：A 包依赖 B 包，按传统发布流程需先发布 B 包至 npm，A 包通过 npm registry 加载 B 包，但 Workspace（Monorepo）直接在 A 包的 `node_modules` 目录下创建指向 B 包源代码的符号链接
- 依赖提升与扁平化：包管理器解析依赖树时执行依赖提升，检测所有子包中共享的、版本兼容的依赖，将其安装于项目根目录下扁平化的 `node_modules` 目录
- pnpm workspace 优化：在 npm/yarn 的扁平化结构中，某包可访问未在其 `package.json` 中声明但在根目录被 依赖提升的依赖，pnpm 维持严格的依赖关系树，在根目录通过硬链接将依赖存储于全局内容可寻址存储，再在各个包的 `node_modules` 中通过符号链接确保其只能访问其 `package.json` 中明确声明的依赖

npm & pnpm：

1. 安装速度：npm 安装依赖比 pnpm 慢，复制完整依赖比复制硬链接和符号链接慢
2. 存储策略：当通过 npm 于不同项目安装相同依赖时，其存储这些依赖于各个项目的 `node_modules` 中，而 pnpm 在根目录通过硬链接将依赖存储于全局内容可寻址存储，依赖无论安装几次，在硬盘上只存储一次，同时在 `node_modules` 中为各个包创建硬链接或符号链接，项目据此访问全局存储的依赖
3. 依赖关系：在 npm/yarn 的扁平化结构中，某包可访问未在其 `package.json` 中声明但在根目录被 依赖提升的依赖，pnpm 维持严格的依赖关系树，在根目录通过硬链接将依赖存储于全局内容可寻址存储，再在各个包的 `node_modules` 中通过符号链接确保其只能访问其 `package.json` 中明确声明的依赖
4. Monorepo 支持 & 兼容性：pnpm 原生支持 Monorepo，兼容性不如 npm，命令参数和选项有所差异

Dev Dependency：在开发测试编译构建必须存在的依赖（测试/构建/格式化/类型定义工具），其不具有传递性；由工具链加载和执行如构建工具打包代码、Babel  编译 ES6 → ES5、Jest  单元测试，其作用在构建阶段结束后完成，不被打包至最终生产 Bundle

Dependency：在生产阶段运行时必须存在的依赖（框架/核心库/核心工具），其具有传递性，当 A 包依赖 B 包，而 B 包依赖 C 包时，C 包自动安装 ​ 并成为 A 包的传递依赖，出现于 A 包的 `node_modules` 中；JavaScript 运行时在项目部署并执行业务逻辑时，通过 `import` 或 `require` 语句加载解析 Dependency 依赖，其被打包至最终生产 Bundle

Module：分割程序所得到的独立片段即模块，各个模块执行特定任务且可被其他模块按需引用
Chunk：由多个 module 组成，代码分割即将代码分割为多个 chunks，按需加载对应的 chunk
Bundle：构建过程的最终输出

通过配置 `module.rules` 匹配指定资源类型及 `url-loader` 和 `style-loader` 将静态资源转换为 Base64 或 CSS 字符串内联至同一 JavaScript Bundle 中

```js
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif|svg|webp)$/,
        use: [
          {
            loader: "url-loader",
            options: {
              limit: 8192,
              encoding: "base64",
            },
          },
        ],
      },
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```