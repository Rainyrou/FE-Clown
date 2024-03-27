在 Webpack 中，修改一个文件是否导致所有文件更新取决于如何配置 Webpack 的输出文件名和如何分割代码。文件名的哈希有助于缓存管理和版本控制，在文件名中包含哈希值可确保当文件内容发生变化时，浏览器能请求到最新文件，而不是从缓存中加载旧版本的文件

1. `[hash]`：基于整个项目的构建生成哈希值，项目中的任何一个文件发生变化，整个项目构建的哈希值均发生变化，适用在每次构建时于所有构建产物均使用相同哈希值
2. `[chunkhash]`：基于每个 chunk 内容生成哈希值，若 chunk 的内容不变，则哈希值保持不变，适用于缓存静态资源。由于只有变化的 chunk 的哈希值会变化，不变的 chunk 仍可利用浏览器缓存
3. `[contenthash]`：基于每个文件内容生成的哈希值，与 `[chunkhash]` 相比，`[contenthash]` 为文件级别的粒度，适用于缓存静态资源，如使用 `MiniCssExtractPlugin` 提取 CSS 到单独文件，确保当 CSS 内容改变时，相关文件的哈希值改变，而改变 JavaScript 文件不会影响 CSS 文件的哈希值

`webpack.config.js`

```js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    path: __dirname + '/dist',
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'styles/[name].[contenthash].css',
    }),
  ],
};
```
