1. 持久化缓存 Persistent Caching: 缓存模块的编译结果，再次构建时直接从缓存中读取，避免重复编译的过程，节省构建时间
2. 模块联邦 Module Federation: 允许多个独立的构建系统在运行时共享模块，而无需重新编译或打包，适用于微前端架构，使不同团队可以独立开发和部署应用的不同部分，然后在客户端动态合并它们。通过模块联邦，不同版本的库或模块可以共存，因为它们是在运行时动态加载的，这使得不同前端应用可以使用自己依赖的特定版本，而不会影响其他应用。模块联邦的核心为 `hosts` 和 `remotes`，宿主指加载远程模块的应用，而远程是指被宿主应用动态加载的独立部署的模块
3. Tree Shaking: 显著改进 Tree Shaking，Webpack5 更有效地识别无用代码，将其从最终的 bundle 中移除，减小打包文件大小
4. 资产模块 Asset Modules: 替代在先前版本使用的 file-loader、url-loader 等 loader。通过资产模块，我们易于将静态资源作为模块引入，且更灵活地控制输出
5. 算法和默认值: Webpack5 改进其内部算法并优化默认配置
6. 环境分离 Environment Splitting: Webpack5 更好地处理不同环境间的配置差异，不同环境配置 Webpack 变得更简单直观

持久化缓存配置：

```js
module.exports = {
  cache: {
    type: 'filesystem', 
    buildDependencies: {
      config: [__filename] 
    }
  }
};
```

模块联邦配置：

* hot：

```js
const ModuleFederationPlugin = require('webpack').container.ModuleFederationPlugin;

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'host',
      remotes: {
        remoteApp: 'remoteApp@http://localhost:3001/remoteEntry.js',
      },
    }),
  ],
};
```

* remote

```js
const ModuleFederationPlugin = require('webpack').container.ModuleFederationPlugin;

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'remoteApp',
      filename: 'remoteEntry.js',
      exposes: {
        './MyComponent': './src/MyComponent',
      },
      shared: require('./package.json').dependencies,
    }),
  ],
};
```