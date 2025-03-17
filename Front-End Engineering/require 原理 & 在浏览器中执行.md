```
require('module')
  → Module._resolveFilename 解析路径
  → 检查 Module._cache 命中缓存则直接返回
  → 创建 Module 实例 new Module(filename, parent)
  → 根据文件类型加载模块 .js/.json/.node
  → 编译执行 vm.runInThisContext
  → return module.exports
```

Node.js 的 `require`  函数用于实现模块依赖加载和作用域隔离，其为  CommonJS 模块系统的核心实现，模块系统源码位于  `lib/module.js`

1. 路径解析：将输入的模块标识符如  `'./my-module'` 解析为绝对路径，对于绝对路径或 Node.js 内置模块，无需解析直接使用，对于第三方模块，递归查找  `node_modules`，从当前目录逐级向上查找
2. 文件定位：若未指定扩展名，依次以  `.js`、`.json` 和 `.node` 补全拓展名并查找文件。若解析到的路径为一个目录，`require` 加载该目录下的 `package.json` 中的 `main` 字段指定的文件，若无 `package.json` 则依次尝试加载 `index.js`、`index.json` 和 `index.node`
3. 缓存检查：`require` 对已加载的模块进行缓存，以模块绝对路径为键，存储  `Module`  实例，每次加载模块时检查缓存中是否已存在该模块，若存在则直接返回缓存中的  `exports`  对象，否则继续加载过程，还可通过  `delete require.cache[filename]`  强制重新加载
4. 创建模块实例：每个模块对应一个  `Module`  实例，其包含  `exports`、`id` 和 `loaded`  等属性
5. 加载和编译模块：对于 `.js`  文件，读取文件内容并包裹为一个函数，通过  `vm.runInThisContext`  编译，函数参数为 `exports`、`require`、`module`、`__filename` 和 `__dirname` 等局部变量，为每个模块提供独立作用域，避免全局污染，对于`.json`  文件，通过 `JSON.parse` 解析其为 JSON 对象，并将结果赋值给  `module.exports`，对于 `.node`  文件，通过  `process.dlopen`  加载 C++ 插件
6. 模块导出：模块通过修改  `module.exports`  或  `exports`  对象导出模块公共接口

在浏览器执行：

浏览器无 Node.js 的  `Module`  对象和模块解析逻辑，没有文件系统去直接读取本地文件，而依赖网络请求加载模块，`require`  为同步操作，而浏览器异步加载资源

1. 构建工具
2. 通过 AMD 异步模块定义如 RequireJS 实现浏览器端模块化，使用  `define`  函数定义模块及其依赖，通过  `<script>`  标签异步加载模块文件，依赖加载完后执行回调，配置模块路径别名，解决浏览器路径问题
3. 通过  `XMLHttpRequest`  或  `fetch`  异步加载模块代码并包裹为函数，以  `eval`  执行
