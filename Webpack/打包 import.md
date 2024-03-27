Webpack 处理 `import` 语句的方式是将它作为模块依赖的一部分来处理。当 Webpack 遇到一个 `import` 语句时，解析器根据 `import` 语句提供的路径信息查找模块，指定模块添加到当前模块的依赖图中，接着解析这些依赖，默认情况下将路径解析为一个文件，若未找到，Webpack 尝试将路径解析为一个目录，并查找该目录下的 `index.js` 文件，最后 Webpack 找到它们的源文件，并将它们打包到最终的 bundle 中。Webpack 本身只能理解 JavaScript，如果 `import` 的模块是非 JavaScript 文件，Loader 可将其他类型文件转换为有效的 module，将之加入到依赖图中


