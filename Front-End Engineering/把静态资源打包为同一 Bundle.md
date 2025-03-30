通过配置 `module.rules` 匹配指定资源类型及 `url-loader` 和 `style-loader` 将静态资源转换为 Base64 或 CSS 字符串内联至同一 JavaScript Bundle 中

```js
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif|svg|webp)$/,
        use: [
          {
            loader: "url-loader",
            options: {
              limit: 8192,
              encoding: "base64",
            },
          },
        ],
      },
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```