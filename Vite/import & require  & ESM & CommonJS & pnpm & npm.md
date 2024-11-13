`import`：ES Module，其为 ES6 引入的模块导入语法，在编译时执行，允许静态分析，支持 Tree Shaking，支持动态导入模块，返回一个 Promise，处理循环依赖时，返回的是导出对象的引用
`require`：CommonJS，其为 Node.js 特有的模块导入语法，在运行时执行，不支持 Tree Shaking，支持动态导入模块，处理循环依赖时，返回的是导出对象的拷贝
ES Module：`import` 和 `export` 模块导入导出语法，在编译时执行，允许静态分析，支持同步和异步加载模块，浏览器原生支持 ESM，通过 `<script type="module">` 直接执行，Node.js 通过 `.mjs` 扩展名或 `package.json` 中的`"type": "module"` 支持 ESM
CommonJS：`require` 和 `module.exports` 模块导入导出语法，在运行时执行，各个文件均为一个 module，有其独立的作用域，同步加载模块，用于 Node.js 环境，其模块系统的实现基于 Node.js 的 `Module` 核心模块

1. 安装速度：pnpm 安装依赖比 npm 快，复制硬链接和符号链接比复制完整依赖快
2. 存储策略：当使用 npm 于不同项目安装相同依赖时，其为每个项目下载和存储这些依赖，而 pnpm 采用内容寻址的全局存储机制，当其安装依赖时并非直接将依赖置于项目的 `node_modules` 中，而存储于全局目录 `~/.pnpm-store`，依赖无论安装几次，在硬盘上只存储一次，pnpm 在 `node_modules` 为各个包创建硬链接或符号链接，项目即可访问全局存储的依赖，降低内存占用
3. 依赖关系：npm 允许子依赖关系的平坦结构，而 pnpm 维持严格的依赖关系树，创建一个含该包所有依赖的独立 `node_modules` 子目录以确保各个包只能访问其 `package.json` 中明确声明的依赖，避免依赖冲突
4. Monorepo 支持：pnpm 原生支持 Monorepo
5. 兼容性：pnpm 较新，兼容性不如 npm，命令参数和选项有所差异
