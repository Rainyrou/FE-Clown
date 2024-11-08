`import`：ES Module，其为 ES6 引入的模块导入语法，在编译时执行，允许静态分析，支持 Tree Shaking，支持动态导入模块，返回一个 Promise，处理循环依赖时，返回的是导出对象的引用
`require`：CommonJS，其为 Node.js 特有的模块导入语法，在运行时执行，不支持 Tree Shaking，支持动态导入模块，处理循环依赖时，返回的是导出对象的拷贝
ES Module：`import` 和 `export` 模块导入导出语法，在编译时执行，允许静态分析，支持同步和异步加载模块，浏览器原生支持 ESM，通过 `<script type="module">` 直接执行，Node.js 通过 `.mjs` 扩展名或 `package.json` 中的`"type": "module"` 支持 ESM
CommonJS：`require` 和 `module.exports` 模块导入导出语法，在运行时执行，各个文件均为一个 module，有其独立的作用域，同步加载模块，用于 Node.js 环境，其模块系统的实现基于 Node.js 的 `Module` 核心模块
