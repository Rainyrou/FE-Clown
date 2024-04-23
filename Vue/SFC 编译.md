Vue Single File Components 即 `.vue` 文件，它允许开发者将一个组件的 Template、JavaScript 脚本和 Style 封装在单个文件中

1. 解析：`.vue` 文件首先被 `vue-loader` 或 Vite 解析成一个 JavaScript 对象
2. 模板编译：`@vue/compiler-sfc` 将 Template 编译成 Render Function
3. 样式处理：Style 部分根据指定的预处理器或原生 CSS 处理，并注入到最终的 JavaScript 包或提取成单独的 CSS 文件
4. 脚本处理：Script 作为一个 module 处理，它可以是 ES6、TypeScript 等现代 JavaScript，通过 Babel、TypeScript 编译器等工具被编译成浏览器兼容的 JavaScript 代码


