1. 安装依赖

```bash
npm install --save-dev babel-loader @babel/core @babel/preset-env
```

2. 配置 Webpack

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
          }
        }
      }
    ]
  }
};
```

3. 配置 Babel

```js
"babel": {
  "presets": [
    ["@babel/preset-env", {
      "targets": "> 0.25%, not dead"
    }]
  ]
}
```