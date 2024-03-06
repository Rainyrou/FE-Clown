Tree-shaking 用于移除 JavaScript 项目中未使用的代码，减小 bundle 体积，它依赖于 ES6 模块的静态结构，允许构建工具在打包时识别并删除未使用的代码

##### Webpack

Webpack 从 2.x 版本开始引入了 Tree-shaking。其核心思想基于 ES6 模块特性，即导入和导出必须是静态的

###### 注意事项

- 只适用于 ES6 模块 `import` 和 `export`，不适用于 CommonJS 的 `require`
- 需在生产模式下运行，确保启用压缩插件

###### 底层原理

1. Webpack 静态解析项目中的 ES6 模块语法 `import` 和 `export`，构建依赖图
2. 在依赖图中，Webpack 标记所有未被其他模块引用的导出
3. 在生产模式下，Webpack 使用 UglifyJS 或 Terser 等压缩工具压缩代码，删除那些被标记为未使用的导出
4. 最终生成的 bundle 变小，因为它不包含项目中未使用的代码

##### Vite

Vite 使用 Rollup 作为其底层打包工具实现 Tree-shaking

###### 注意事项

- Rollup 的 Tree-shaking 比 Webpack 更高效，这是因为 Rollup 专注于 ES6 模块，且它的打包算法在 Tree-shaking 方面做了优化
- Vite 在开发环境下使用原生 ES6 模块加载，加快了 dev server 的启动。在生产环境下，利用 Rollup 的 Tree-shaking 特性进行优化

###### 底层原理

1. Rollup 基于 ES6 模块的静态结构进行代码分析和打包
2. 与 Webpack 类似，Rollup 静态解析项目中的 ES6 模块语法 `import` 和 `export`，构建依赖图
3. Rollup 在打包过程中删除那些未被其他模块引用的导出
4. 最终生成的 bundle 变小，因为它不包含项目中未使用的代码
