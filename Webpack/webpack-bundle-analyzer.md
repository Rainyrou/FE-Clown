`webpack-bundle-analyzer` 是一个 Webpack 插件，它提供一个可视化交互式的树形图分析 Webpack 打包后的文件大小，我们可以看到每个 chunk 和 module 的大小，及它们在构建产物体积中所占比例，同时对比压缩前后的 chunk 和 module 大小，我们可以根据模块路径筛选文件，定位特定的 chunk、module 或第三方库

```bash
npm install --save-dev webpack-bundle-analyzer
```

`webpack.config.js`

```js
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin()
  ]
};
```

- **`analyzerMode`**: 设置插件模式，默认为 `server`，在本地启动一个 HTTP 服务器查看报告。其他选项包括：`static`（生成 HTML 文件）和 `disabled`
- **`analyzerPort`**: 设置 `server` 模式下服务器端口，默认为 `8888`。
- **`reportFilename`**: 在 `static` 模式下，设置生成报告的文件名，默认为 `report.html`
- **`openAnalyzer`**: 控制是否在浏览器中自动打开报告，默认为 `true`


