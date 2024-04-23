Tree-Shaking 是一种通过移除 JavaScript 中未被引用代码的方式来减少最终打包体积的过程

1. 模块化架构

Vue3 采用更加模块化的架构，其功能和 API 都被设计为可按需导入的模块，此种方式有利于打包工具的静态分析，从而实现 Tree-Shaking

2. Composition API & 静态导入

Vue3 引入的组合式 API 进一步促进 Tree-Shaking 的效果。与 Vue2 的选项式 API 相比，组合式 API 让我们按需引入所需使用的 API，如 `ref` 和 `reactive` 等，显著减少最终包的体积。此外 Tree-Shaking 的效果在很大程度上依赖于 ESM 的静态结构，因为它允许打包工具在编译时就确定导出是否使用

3. 构建工具

Vite 或 Webpack 等前端构建工具能够识别和移除未使用的 Vue 模块，尤其是在生产阶段，通过压缩和优化步骤进一步减少最终的打包体积

