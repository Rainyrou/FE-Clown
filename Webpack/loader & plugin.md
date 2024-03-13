##### loader

Webpack 本身只能理解 JavaScript，Loader 可将其他类型文件转换为有效的 module，将之加入到依赖图中，以供应用程序使用

1. babel-loader: 兼容旧浏览器，将 ES6 代码转换为 ES5
2. ts-loader：加载 TypeScript 文件，并将其转换为 JavaScript
3. vue-loader: 加载 Vue SFC 即 `.vue` 文件
4. style-loader: 将模块的导出作为样式添加到 DOM 中
5. css-loader: 处理 CSS 文件中的 `import` 和 `url()`，并将其转换为 JavaScript 模块
6. sass-loader: 将 Sass/Scss 文件编译为 CSS
7. less-loader：将 Less 文件编译为 CSS
8. file-loader: 解析 `import`/`require()` 的文件，并将它们导出到构建目录
9. url-loader:与 `file-loader` 类似，但如果文件小于限制，则返回 data URL

```JavaScript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: 'babel-loader',
        exclude: /node_modules/
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/,
        use: ['file-loader']
      },
      {
        test: /\.vue$/,
        use: 'vue-loader'
      },
      {
        test: /\.ts$/,
        use: 'ts-loader'
      }
    ]
  }
};
```

###### 底层原理

1. 转换过程：在遇到由 `import` 或 `require` 引入的非 JavaScript 文件时，Webpack 会查看其配置中的 `module.rules`，找到相应的 loader 对文件进行处理
2. 链式调用：多个 loader 可以链式调用。链中的每个 loader 都可以对资源进行转换，且链中的前一个 loader 的返回结果传递给下一个 loader。最终，链中的最后一个 loader 返回 JavaScript 给 webpack
3. 配置：通过在 Webpack 配置文件中以数组形式定义 `rules`，指定哪些文件应该被哪些 loader 处理。`test` 属性用于指定哪些文件被转换，`use` 属性指定使用哪个 loader

##### Plugin

Plugin 是基于 Webpack 的事件钩子系统构建的，Plugin 提供了一种强大的机制来直接参与到 Webpack 的打包过程，允许在构建中的特定时刻添加自定义构建步骤

1. HtmlWebpackPlugin: 为应用程序生成 HTML 文件，并自动注入所有生成的 bundle
2. MiniCssExtractPlugin: 将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件
3. OptimizeCSSAssetsPlugin：压缩 CSS
4. UglifyjsWebpackPlugin & TerserWebpackPlugin：压缩 JavaScript
5. CompressionWebpackPlugin：压缩版本的资源，如 Gzip 或 Brotli
6. DefinePlugin: 允许在编译时创建全局常量，如 `process.env.NODE_ENV`
7. CleanWebpackPlugin: 在构建前清理/删除构建文件夹，如构建前清理 `dist` 文件夹
8. CopyWebpackPlugin: 将单个文件或整个目录复制到构建目录
9. HotModuleReplacementPlugin：启用 HMR，在开发环境中实现页面实时预览

```JavaScript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const webpack = require('webpack');

module.exports = {
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new MiniCssExtractPlugin({
      filename: '[name].css'
    }),
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
    })
  ]
};
```

###### 底层原理

1. Webpack 有一个基于事件的 Plugin 系统，Plugin 可以监听构建过程中的不同事件节点，并在这些节点上执行特定操作
2. Plugin 可以访问 Webpack 的内部实例，包括 Compiler & Compilation 对象。这些对象提供对 Webpack 运行时和构建流程的完全访问权限，Compiler 对象代表完整的 Webpack 环境配置，它负责启动并协调整个编译过程。Compilation 对象代表一次资源版本构建
3. Plugin 通过 Webpack 配置文件的 `plugins` 数组进行配置和使用。Plugin 一般通过 `new` 关键字来创建实例
4. 自定义 Plugin 包括一个 JavaScript 类，类的 `apply` 方法被 Webpack 编译器调用，Plugin 可在 `apply` 方法中注册自定义的行为
