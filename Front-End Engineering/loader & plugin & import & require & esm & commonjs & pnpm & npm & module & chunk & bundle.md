Loader：用于将其他格式的文件转换为 Webpack 支持打包的 module 即模块化非 JavaScript，通过转义以兼容旧版本浏览器及跨平台支持并减少构建产物打包体积，其本质为函数，支持同步、异步操作及链式调用，Webpack 检查 `import` 或 `require` 引入的非 JavaScript 文件配置中的 `module.rules` 并以相应 Loader 进行处理 ，Webpack 打包时按数组从后往前的顺序将目标资源交由 Loader 处理

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
6. url-loader：用于处理图片，目标图片若大于指定大小则将其打包，否则将其转换为 base64 字符串并合并至 JavaScript 文件中

Plugin：基于 Tapable 的 Webpack 事件钩子系统，用于在构建的各个阶段添加自定义的构建步骤。Webpack 的构建过程可视为生产线，从源文件经过多个处理阶段最终转换为输出结果，各个处理阶段职责单一且存在依赖关系，只有完成当前处理阶段，结果才能交由下一阶段继续处理，其保证构建的有序性，而 Plugin 可视为插入到该生产线中的功能模块，其通过监听 Webpack 的事件流机制在构建的特定阶段对资源进行定制化处理

1. copy-webpack-plugin：用于将单文件或完整目录复制到构建目录
2. html-webpack-plugin: 用于动态生成 HTML 文件并自动引入打包后生成的 bundle
3. mini-css-extract-plugin: 用于从 JavaScript 文件中提取 CSS，生成独立于该 JavaScript 文件的 CSS 文件
4. optimize-css-assets-plugin：压缩 CSS
5. terser-webpack-plugin：压缩 JavaScript

`import`：ES Module，其为 ES6 引入的模块导入语法，在编译时执行，允许静态分析，支持 Tree Shaking，支持动态导入模块，返回一个 Promise，处理循环依赖时，返回的是导出对象的引用
`require`：CommonJS，其为 Node.js 特有的模块导入语法，在运行时执行，不支持 Tree Shaking，支持动态导入模块，处理循环依赖时，返回的是导出对象的拷贝
ES Module：`import` 和 `export` 模块导入导出语法，在编译时执行，允许静态分析，支持同步和异步加载模块，浏览器原生支持 ESM，通过 `<script type="module">` 直接执行，Node.js 通过 `.mjs` 扩展名或 `package.json` 中的`"type": "module"` 支持 ESM
CommonJS：`require` 和 `module.exports` 模块导入导出语法，在运行时执行，各个文件均为一个 module，有其独立的作用域，同步加载模块，用于 Node.js 环境，其模块系统的实现基于 Node.js 的 `Module` 核心模块

1. 安装速度：pnpm 安装依赖比 npm 快，复制硬链接和符号链接比复制完整依赖快
2. 存储策略：当使用 npm 于不同项目安装相同依赖时，其为每个项目下载和存储这些依赖，而 pnpm 采用内容寻址的全局存储机制，当其安装依赖时并非直接将依赖置于项目的 `node_modules` 中，而存储于全局目录 `~/.pnpm-store`，依赖无论安装几次，在硬盘上只存储一次，pnpm 在 `node_modules` 为各个包创建硬链接或符号链接，项目即可访问全局存储的依赖，降低内存占用
3. 依赖关系：npm 允许子依赖关系的平坦结构，而 pnpm 维持严格的依赖关系树，创建一个含该包所有依赖的独立 `node_modules` 子目录以确保各个包只能访问其 `package.json` 中明确声明的依赖，避免依赖冲突
4. Monorepo 支持：pnpm 原生支持 Monorepo
5. 兼容性：pnpm 较新，兼容性不如 npm，命令参数和选项有所差异

Module：分割程序所得到的独立片段即模块，各个模块执行特定任务且可被其他模块按需引用
Chunk：由多个 module 组成，代码分割即将代码分割为多个 chunks，按需加载对应的 chunk
Bundle：构建过程的最终输出