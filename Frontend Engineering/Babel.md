1. 转译语法

Babel 能够识别新的 JavaScript 语法并将之转换为旧版 JavaScript 语法，这使我们能利用最新的语法特性编写代码，而无需担心兼容性问题

2. Polyfills

对于新的 API 如 Promise、`Array.from` 等和全局对象如 Map、Set 等，仅通过语法转换是远远不够的，因为它们在旧环境中不存在。Babel 使用称为 polyfills 的代码片段来添加缺失的功能，通过 `core-js` 和 `regenerator-runtime` 实现，它根据目标环境引入必要的 polyfills

3. Plugins and Presets

Babel 的功能可以通过插件扩展，它有大量用于特定转译任务的插件。此外，为简化配置，相关插件被打包成预设如`@babel/preset-env`。`@babel/preset-env` 能根据目标环境自发决定需要使用的 Babel 插件和 polyfills，只有必要的转换和 polyfills 会被包含在最终的代码中，优化输出代码并减少不必要的转换



