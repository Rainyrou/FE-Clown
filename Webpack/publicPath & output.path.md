`publicPath`：指定浏览器中被引用资源的发布 URL 的路径前缀，它可以是一个相对/绝对路径或完整 URL，影响的是资源的访问路径，不同于`output.path` 只用于构建阶段，`publicPath` 影响的是运行时的行为

`output.path` 指定 Webpack 输出文件目录即最终构建产物存放的物理路径，这个路径是本地文件系统上的绝对路径

```js
const path = require('path');

module.exports = {
  output: {
    path: path.resolve(__dirname, 'dist'), 
  },
};
```

- 加载资源：`publicPath` 指定加载静态资源的基础路径
- CDN 部署：设置 `publicPath` 为 CDN 的 URL 可确保资源正确加载并托管到 CDN。
- 路由：`publicPath` 可处理 HTML5 History API 的单页应用路由

```js
module.exports = {
  output: {
    publicPath: '/assets/',
  },
};
```

1. 动态设置 `publicPath`：

```js
__webpack_public_path__ = myDynamicPath;
```

2. 环境变量

```js
module.exports = {
  output: {
    publicPath: process.env.NODE_ENV === 'production' ? 'https://cdn.example.com/assets/' : '/assets/',
  },
};
```

3. CDN：

```js
module.exports = {
  output: {
    publicPath: 'https://cdn.example.com/assets/',
  },
};
```

