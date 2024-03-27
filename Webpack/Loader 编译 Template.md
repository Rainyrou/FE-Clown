模板编译：将特定语法的模板如 Vue 的 `.vue` 文件或 React 的 JSX 转换成浏览器可以理解的 JavaScript 代码，Webpack 通过 loader 机制支持对模板的编译

1. Vue

```bash
npm install -D vue-loader vue-template-compiler
```

`webpack.config.js`

```js
const VueLoaderPlugin = require('vue-loader/lib/plugin');

module.exports = {
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      },
    ]
  },
  plugins: [
    new VueLoaderPlugin()
  ]
};
```

2. React

```bash
npm install -D babel-loader @babel/core @babel/preset-env @babel/preset-react
```

`webpack.config.js`

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react']
          }
        }
      },
    ]
  }
};
```