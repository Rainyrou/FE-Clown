>[!todo] To do 
>尤大十几年前写的 `vue-dev-server`，可以视为 Vite 的雏形，录个视频讲一下

[GitHub - vuejs/vue-dev-server: A POC dev server that allows you to import \`\*.vue\` files via native ES modules imports.](https://github.com/vuejs/vue-dev-server)

#### 底层原理

- Imports are requested by the browser as native ES module imports - there's no bundling.
- The server intercepts requests to `*.vue` files, compiles them on the fly, and sends them back as JavaScript.
- For libraries that provide ES modules builds that work in browsers, just directly import them from a CDN.
- Imports to npm packages inside `.js` files (package name only) are re-written on the fly to point to locally installed files. Currently, only `vue` is supported as a special case. Other packages will likely need to be transformed to be exposed as a native browser-targeting ES module.

![[1699328333412.png]]

#### 初始化

```bash
git clone https://github.com/vuejs/vue-dev-server.git
cd vue-dev-server
yarn
```

一般是先从 `package.json` 文件开始看源码：

![[1699328766086.png]]

据此我们查看 `test` 文件夹，三个文件代码量不大

然后是 `bin/vue-dev-server.js`

![[1699329011961.png]]

重点在  `vueMiddleware`  中间件，开始调试这个中间件

#### 调试源码

找到  `package.json`  的  `scripts`，把鼠标移动到  `test`  命令上，选择第二个

![[1699329341781.png]]
，
在 `bin/vue-dev-server.js` 的对应行先打下断点，按住 F11 可以进入 `vueMiddleware`  函数

![[1699329544694.png]]

![[1699329811672.png]]

在浏览器打开 `http://localhost:3000`

![[1699329886807.png]]

#### 核心功能

不在调试的状态下，我们可以在  `bin/vue-dev-server.js`  文件中注释掉  `app.use(vueMiddleware())`，在命令行执行  `npm run test`，在浏览器打开  `http://localhost:3000`

![[1699330344431.png]]

再启用中间件后，浏览器渲染成功，引入 Vue 的形式发生改变

![[1699330611926.png]]

#### 源码解读

##### transformModuleImports.js

```JavaScript
const recast = require('recast')
const isPkg = require('validate-npm-package-name')
```

1. `recast`: 一个 JavaScript 语法树工具，能够解析代码字符串生成 AST（Abstract Syntax Tree），然后你可以遍历和修改 AST，并最后将其重新转换为代码字符串，这使得对代码的结构化处理成为可能
2. `isPkg`: 一个函数，用于验证一个字符串是否是有效的 npm 包名。它在这里用来检测 `import` 语句中的源是否是一个 npm 包

```JavaScript
function transformModuleImports(code) {
  const ast = recast.parse(code)
  recast.types.visit(ast, {
    visitImportDeclaration(path) {
      const source = path.node.source.value
      if (!/^\.\/?/.test(source) && isPkg(source)) {
        path.node.source = recast.types.builders.literal(`/__modules/${source}`)
      }
      this.traverse(path)
    }
  })
  return recast.print(ast).code
}
```

1. `recast.parse(code)`: 将传入的代码字符串解析为 AST。在 AST 中，代码被表示为嵌套的节点，每个节点代表代码的一个结构（如语句、表达式等）
2. `recast.types.visit` 函数遍历 AST 中的每个节点，并对每个节点调用 `visitImportDeclaration` 访问器方法专门查找 `import` 声明
3. `visitImportDeclaration(path)`: 一个访问器方法，它在每个 `import` 声明上被调用。参数 `path` 包含了当前访问的 AST 节点信息
4. `const source = path.node.source.value`: 获取 `import` 语句中源模块的路径值
5. `if (!/^\.\/?/.test(source) && isPkg(source))`: 检查 `import` 的源路径是否不是相对路径（即不是以 `.` 或 `..` 开头）并且是一个有效的 npm 包名。如果满足条件，修改 `import` 声明的源路径，前缀为 `/__modules/`。这样的修改用于将这些 `import` 重定向到一个特定的处理模块的路径，从而能够处理对 npm 包的请求
6. `this.traverse(path)`: 在每个 `import` 声明处理完后，这个调用继续遍历 AST 的其余部分
7. `return recast.print(ast).code`: 在对 AST 做出修改后，将修改后的 AST 转换回代码字符串并返回

AST 是源代码的树状表示，使得我们可以进行精确地进行代码分析和修改。`recast` 库让我们不仅可以修改代码的结构，同时还保留了代码的原始格式（例如缩进和空格）。在这个函数中，AST 被用来安全地修改 `import` 路径，而不用担心破坏代码的其他部分

##### loadPkg.js

```JavaScript
const fs = require('fs')
const path = require('path')
const readFile = require('util').promisify(fs.readFile)
```

- `fs` 用于文件系统操作
- `path` 用于处理和转换文件路径
- `readFile` 一个通过 `util.promisify` 方法将 `fs.readFile` 方法转换成返回 promise 的异步函数，这允许使用 `async/await` 语法读取文件

```JavaScript
async function loadPkg(pkg) {
  if (pkg === 'vue') {
    const dir = path.dirname(require.resolve('vue'))
    const filepath = path.join(dir, 'vue.esm.browser.js')
    return readFile(filepath)
  }
  else {
    // TODO
    // check if the package has a browser es module that can be used
    // otherwise bundle it with rollup on the fly?
    throw new Error('npm imports support are not ready yet.')
  }
}
```

1. 判断传入的 `pkg` 参数是否等于 `'vue'`。如果满足条件，使用 `require.resolve` 查找 Vue.js 包的路径。`require.resolve` 是 Node.js 的一个方法，用于查找模块的绝对路径，而不实际导入它
2. `path.dirname` 获取到 Vue.js 包的目录路径
3. `path.join` 连接目录路径和 `'vue.esm.browser.js'` 文件名，这是 Vue.js 的一个针对浏览器的 ES module 版本。
4. 使用 `readFile` 读取该文件并返回文件内容。由于 `readFile` 被转换成了一个 promise 返回的函数，所以可以使用 `await` 等待文件读取的完成
5. 如果 `pkg` 不是 `'vue'`，代码抛出一个错误。目前这个函数仅支持加载 Vue.js，对于其他 npm 包的支持还未实现。实际上尤大在注释中提到了两个未来（也就是现在）可能添加的功能：

   - 检查包是否有可用的浏览器 ES module
   - 如果没有，可能会动态地使用如 `rollup` 这样的工具来打包模块

在 Node.js 中，`require.resolve` 方法用于确定模块的绝对路径，而无需执行或导入模块本身。这使得我们可以确定模块文件的位置，并用其他方式读取或处理它们。`path` 和 `fs` 模块是进行文件系统操作的基础工具。`path` 提供跨平台的路径字符串处理功能，而 `fs` 提供了文件读写的 API

##### readSource.js

`readSource.js` 文件中定义了一个 `readSource` 异步函数，其主要作用是读取请求 URL 对应的文件源代码以及该文件的最后修改时间

```JavaScript
const path = require('path')
const fs = require('fs')
const readFile = require('util').promisify(fs.readFile)
const stat = require('util').promisify(fs.stat)
const parseUrl = require('parseurl')
const root = process.cwd()
```

- `readFile`: 使用 `util.promisify` 将 `fs.readFile` 方法转换为返回 Promise 对象的版本，以支持 `async/await` 语法

* `stat`: 使用 `util.promisify` 将 `fs.stat` 方法转换为 Promise 版本，用于获取文件状态信息
* `parseUrl`: 一个解析 URL 的工具函数，来自 `parseurl` 包，用于提取 URL 中的路径
* `root`: 使用 `process.cwd()` 获取当前工作目录的路径

```JavaScript
async function readSource(req) {
  const { pathname } = parseUrl(req)
  const filepath = path.resolve(root, pathname.replace(/^\//, ''))
  return {
    filepath,
    source: await readFile(filepath, 'utf-8'),
    updateTime: (await stat(filepath)).mtime.getTime()
  }
}
```

1. 从请求对象 `req` 中通过 `parseUrl` 获取到请求的路径名 `pathname`
2. 使用 `path.resolve` 将 `root`（当前工作目录）和处理过的 `pathname` 结合，生成文件的绝对路径 `filepath`。这里使用正则表达式 `replace(/^\//, '')` 去掉路径前面的斜杠，以防止路径解析错误
3. 调用 `readFile` 异步读取文件内容，这里指定字符编码为 `'utf-8'`，使得返回的是字符串而不是原始的 buffer
4. 使用 `stat` 获取文件的状态信息，并从中提取文件的最后修改时间 `mtime`，然后通过 `getTime()` 将其转换为时间戳（毫秒表示）

##### middleware.js

`middleware.js` 文件定义了一个名为 `vueMiddleware` 中间件，用于处理 Vue SFC 的编译、缓存和响应逻辑，JavaScript 模块的导入转换，以及对特定 npm 包的加载。此中间件集成到 Express 中，可以有效地编译 `.vue` 文件，处理 ES Module 导入，以及提供 npm 包，优化响应时间和服务器性能

这个实现充分体现前端工程化的一些核心概念，比如模块化开发、代码转换和转译、sourcemap 生成以及缓存策略

首先导入一些 Node 的内建模块、第三方模块和自定义模块，接着定义一个名为 `vueMiddleware` 的函数，这个函数返回一个异步中间件函数。这个中间件负责处理所有 `.vue`、`.js` 文件和 `/__modules/` 路径的请求

1. **缓存初始化**:

   - 使用 `lru-cache` 模块初始化一个缓存对象。这是一个简单的 LRU（最近最少使用）缓存，用于存储编译后的组件，以避免每次请求都重新编译

2. **编译器初始化**:

   - 创建 Vue 默认编译器实例

3. **辅助函数定义**:

   - `send`: 发送响应给客户端的函数
   - `injectSourceMapToBlock`: 给 JavaScript 或 CSS 代码块注入 sourcemap 的函数
   - `injectSourceMapToScript` 和 `injectSourceMapsToStyles`: 这两个函数分别用于给样本和样式注入 sourcemap
   - `tryCache`: 尝试从缓存获取数据的函数，如果数据自缓存时间以来已更改，则返回 `null`
   - `cacheData`: 将数据缓存起来的函数

Source map 用于在调试时将编译后的代码映射回原始源代码，这对于在开发过程中定位问题非常有用

4. **bundleSFC**:

   - 一个异步函数，用于编译.vue 文件。它首先读取源文件，然后编译成描述符，最后使用 `sourcemap` 将脚本和样式组装成最终的组件代码

5. **中间件函数**:

   - 这个返回的异步函数就是中间件本身。它检查请求的路径，根据路径的不同进行不同的处理：
     - 如果是 `.vue` 文件，它会尝试从缓存获取编译好的代码，如果没有缓存，则读取源文件，使用 `vueCompiler` 编译 `.vue` 文件，并缓存结果，将编译后的 JavaScript 代码发送给客户端
     - 如果是 `.js` 文件，它会尝试从缓存中获取，如果没有缓存，则读取 JavaScript 源码，并调用 `transformModuleImports` 函数转换 ES Module 的导入路径并缓存结果，将转换后的代码发送给客户端
     - 如果请求路径以 `/__modules/` 开头，它会尝试从缓存中获取，不检查更新时间（`checkUpdateTime = false`），如果没有缓存，则使用 `loadPkg` 函数来加载 npm 包内容并缓存结果，将加载后的内容发送给客户端

6. **next()调用**:

   - 如果请求的路径不是上述定义的任何一种类型，则调用 `next()`，将控制权传递给下一个中间件
