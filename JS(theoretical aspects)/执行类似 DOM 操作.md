Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时环境，在服务端运行 JavaScript 代码，其无 DOM 树，本身不提供 DOM 或类似浏览器的 API，但可通过第三方库实现类似 DOM 操作的功能：

1. `jsdom`：纯 JavaScript 实现的 HTML 和 DOM 标准库，其通过解析 HTML 构建一个虚拟 DOM 树，提供标准的 DOM API 来操作该虚拟 DOM 树，它还模拟浏览器的事件系统、CSSOM 等特性，尽可能接近真实的浏览器环境，使我们可在 Node.js 中使用类似于浏览器的 DOM 操作，用于服务端渲染、网页抓取、单元测试等
2. `cheerio`：底层使用 `htmlparser2` 解析 HTML 字符串并构建其 DOM 表示，提供一个类似于 jQuery 的 API 操作 HTML，用于需大量 DOM 操作但无需完整浏览器环境的场景
