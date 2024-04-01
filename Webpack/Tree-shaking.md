Webpack 从 2.x 版本开始引入 Tree-shaking，其核心思想基于 ES6 模块特性，即导入和导出必须是静态的

###### 注意事项

- 只适用于 ES6 模块 `import` 和 `export`，不适用于 CommonJS 的 `require`
- 需在生产模式下运行，确保启用压缩工具

###### 底层原理

1. Webpack 静态解析项目中的 ES6 模块语法 `import` 和 `export`，构建依赖图
2. 在依赖图中，Webpack 标记所有未被其他模块引用的导出
3. 在生产模式下，Webpack 使用 UglifyJS 或 Terser 等压缩工具压缩代码，删除那些被标记为未使用的导出
4. 最终生成的 bundle 变小，因为它不包含项目中未使用的代码