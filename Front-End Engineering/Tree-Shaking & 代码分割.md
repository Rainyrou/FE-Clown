DCE（Dead Code Elimination） 用于删除不被执行、对代码执行结果无影响和仅影响死变量的代码

死代码：

- 无法到达：在某些条件下如 `return` 语句后永远无法执行的代码
- 无用计算结果：如未被使用的函数返回值
- 死变量：只写不读的变量

在传统编程语言如 C 和 C++中，DCE 发生于编译阶段，编译器通过解析 AST，检测并删除死代码，而 JavaScript 与传统编程语言的区别在于其为动态类型语言，在浏览器或 Node.js 环境执行代码，因此浏览器或 Node.js 环境本身不执行 DCE，而 Tree-Shaking 本质上是 DCE 的现代实现，JavaScript 的 Tree-Shaking 通过构建工具完成，但 Tree-Shaking 专门针对模块化代码，ES Module 的依赖关系是确定的，在编译时执行，支持静态分析即在不执行代码的情况下分析模块的导入导出以判断哪些部分是可达的，哪些部分是无用的，ES Module 的静态结构（模块的依赖关系在编译时是已知的）使得 Tree-Shaking 成为可能，相比 DCE，Tree-shaking 更关注于消除未被引用的代码，通过解析模块的导入导出，识别模块间的依赖关系并构建模块依赖图，遍历所有模块并以 `usedExports` 标记未被引用的模块导出，在构建时移除那些未被使用的 JavaScript 代码，减少构建产物大小

局限性：

- 副作用：若模块有副作用，即使其导出未被引用，构建工具也无法删除它，为避免误删，Webpack 允许我们通过在 `package.json` 中配置 `sideEffects` 表示模块是否有副作用

```json
"sideEffects": ["*.css", "*.js"]
```

- 动态导入和 CommonJS：若使用动态 `import` 或 CommonJS 的 `require`，Tree-shaking 无法分析其依赖关系

Webpack 启用 Tree-Shaking：

1. 以 ESM 规范编写模块代码
2. 配置

```js
module.exports = {
  entry: "./src/index",
  mode: "production", // 设置为生产模式
  devtool: false,
  optimization: {
    usedExports: true, // 启用 Tree-shaking
    minimize: true, // 启用代码压缩
    minimizer: [new TerserPlugin()],
  },
};
```

代码分割：通过静态分析获取模块间的依赖关系，哪些模块是可拆分的，哪些模块是相互依赖的，哪些模块是共享的，哪些模块是首屏加载所需的，根据模块及其依赖关系所生成的依赖图采取以下策略进行代码分割：

- 入口分割：应用程序有多个入口点如首页、登录页和后台管理页等，通过入口分割将不同页面代码分离，为每个入口点生成一个独立的 Chunk，每个 Chunk 只包含该入口点所需代码
- 动态导入：通过动态 `import` 按需加载，当代码执行至动态 `import` 部分时，Webpack 或 Vite 生成一个独立的 Chunk 并在运行时加载该 Chunk
- 共享模块分割：Webpack `optimization.splitChunks` 和 Vite `rollupOptions.manualChunks` 将多个模块间共享的部分提取为一个独立的 Chunk，避免重复加载相同模块

生成 Chunk 后，Webpack 或 Vite 为每个 Chunk 生成一个对应的文件名，其为一个与文件内容和元数据相关的哈希值（确保文件内容变化时生成不同文件以避免浏览器缓存问题），文件被输出到指定目录如 `dist`
