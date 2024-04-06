1. 转换语法：识别并转换最新的JavaScript 语法为旧版JavaScript 语法，我们无需担心兼容性问题
2. Polyfills：对于Promise、`Array.from` 等ES新特性和Map、Set 等全局对象，仅通过转换语法远远不够，因为它们不存在于旧环境中，Babel 使用 polyfills 代码片段并通过 `core-js` 和 `regenerator-runtime` 添加缺失功能
3. Plugins and Presets



