- 解决开发环境的跨域问题：通过代理 API 请求到后端服务器，避免直接请求时可能遇到的 CORS 错误
- 模拟生产环境的请求处理
- 简化 API 请求路径：通过路径重写等高级配置，简化前端代码中的 API 请求路径

```bash
npm install --save-dev webpack-dev-server
```

`webpack.config.js`

```js
devServer: {
  proxy: {
    '/api': {
      target: 'http://example.com',
      pathRewrite: {'^/api' : ''},
      changeOrigin: true,
      secure: false,
      headers: {
        'X-Added-Header': 'value'
      }
    },
  },
};
```

```bash
npx webpack --config webpack.config.js
```

- 所有的 `/api` 请求均被代理到 `http://example.com`
- 代理仅在开发环境下有效，因为它是通过 `webpack-dev-server` 实现的。生产环境下需通过服务端配置来解决跨域问题，如配置 Nginx 反向代理
