Webpack 通过使用 loader 转换文件，本质上是一个模块转换的过程。每个 loader 都接收前一个 loader 传递的内容，处理后再传递给下一个 loader，直到最后一个 loader 将处理后的结果返回给 Webpack，然后 Webpack 根据配置将处理后的模块打包成最终的静态资源。Webpack 使用 `style-loader`、`css-loader` 及 `less-loader` 来处理 Less

1. 安装

```bash
npm install --save-dev less less-loader css-loader style-loader
```

2. 配置 Webpack

在 `webpack.config.js` 添加这些 loader 到 `module.rules` 数组中。Webpack 会按照数组中 loader 的逆序来处理文件，因此 `less-loader` 被放在最后，接着 `css-loader` 处理得到的 CSS 文件，最终 `style-loader` 将 CSS 样式注入到 DOM 中

- 处理 Less 文件：当 Webpack 遇到 `import './style.less'` 或任何其他方式引入 `.less` 文件时，它使用`less-loader` 将 Less 文件转换为 CSS
- 转换 CSS 为 JS 模块：`css-loader` 解析 CSS 文件中的 `@import` 和 `url()` 等，将 CSS 转换为 JS 模块
- 动态注入 CSS 到 DOM：`style-loader` 将生成的 CSS 作为 `<style>` 标签动态注入到页面的 `<head>` 部分

```JavaScript
module.exports = {
  module: {
    rules: [
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          'less-loader'
        ]
      }
    ]
  }
};
```
