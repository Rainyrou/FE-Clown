1. 基本配置

`webpack.config.js`

```js
const path = require('path');

module.exports = {
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

目录结构：

```
/dist
  bundle.js
```

2. 多入口配置

`webpack.config.js`

```js
module.exports = {
  entry: {
    app: './src/app.js',
    admin: './src/admin.js',
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

目录结构：

```
/dist
  app.js
  admin.js
```

3. `[contenthash]` 优化缓存

`webpack.config.js`

```js
module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

目录结构：

```
/dist
  app.abcdef123456.js
  admin.123456abcdef.js
```

4. 分离静态资源

`webpack.config.js`

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset/resource',
        generator: {
          filename: 'images/[hash][ext][query]',
        },
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'css/[name].[contenthash].css',
    }),
  ],
};
```

目录结构：

```
/dist
  /css
    app.abcdef123456.css
    admin.123456abcdef.css
  /images
    xyz123.png
  app.abcdef123456.js
  admin.123456abcdef.js
```

