Browser Object Model：连接 JavaScript 与浏览器窗口，JavaScript 据此控制浏览器行为

1. `window`：浏览器全局对象（在 Node.js 中为 `global`）和浏览器窗口对象，全局对象即对浏览器运行环境的抽象，其管理全局命名空间并提供对全局变量和函数的访问，浏览器窗口对象包含 `location`、`document`、`history`、`navigator`、`console`、`screen`、`innerWidth`、`parent`、`top`、`frames` 和 `localStorage` 等属性，`hashchange`、`load`、`resize`、`focus`、`blur`、`online` 和 `error` 等事件，`alert`、`confirm`、`prompt`、`open`、`close`、`setTimeout`、`setInterval`、`scrollTo`、`addEventListener` 和 `dispatchEvent` 等。综上，`window` 的属性、事件和方法并非全部源于自身，而有相当部分来自继承，`window` 为实例对象，通过 `Window` 类构造，而 `Window` 类继承 `EventTarget` 类，因此 `window`  对象有 `Window` 类操作浏览器窗口的功能和 `EventTarget` 类的事件处理能力

```
window 对象 -> Window 类 -> EventTarget 类
```

2. `document`：当前浏览器窗口对应的文档对象如 HTML 和 XML
3. `location`：浏览器相关的 URL 信息

属性：

- `href`：完整 URL
- `protocol`：协议
- `host`：主机名 + 端口号
- `hostname`：主机名
- `port`：端口号
- `pathname`：路径部分如 `/user/10086`
- `search`：查询参数
- `hash`：哈希

方法：

- `reload`：重新加载页面
- `replace(url)`：导航至新 URL，不保存当前页面，无法通过后退按钮返回
- `assign(url)`：导航至新 URL，保存当前页面，通过后退按钮返回

4. `history`：操作浏览器的历史记录如前进和后退

属性：

`state`：当前保存的会话状态
`length`：会话记录条数

方法：

`forward`：前进即 `history.go(1)`
`back`：返回即 `history.go(-1)`
`go`：导航至某页
`pushState`：在不重新加载页面的情况下导航至指定 URL，添加新的历史会话于浏览器历史记录栈
`replaceState`：在不重新加载页面的情况下导航至新 URL，替换当前历史会话

Document Object Model：与平台和语言无关的标准接口规范，为 HTML 和 XML 文档提供结构化表述并定义程序动态访问和更新文档结构、内容和样式的方式 ​，在底层上 HTML 为静态文本标记（源码），DOM 为 HTML 在内存中的动态对象表示（解析结果）​，​ 在浏览器渲染中 HTML 元素转换为对应 DOM 节点，而 HTML 属性转换为 DOM 节点属性

节点类型：

* 元素节点（nodeType=1）：HTML 标签
* 属性节点（nodeType=2）：元素属性​​
* 文本节点（nodeType=3）：文本内容，节点名称为 '#text'​​
* CDATA节点（nodeType=4）：XML 的 CDATA
* 注释节点​​（nodeType=8）：HTML 注释
* 文档节点（nodeType=9）：DOM 树根节点，节点名称为 '#document'​​
* 文档类型节点（nodeType=10）：文档类型，节点名称为 doctype 名称
* 文档片段节点（nodeType=11）​​

VDOM：虚拟 DOM 为解决浏览器性能问题而生，其为树形结构的 JavaScript 对象即在 JavaScript 模拟 DOM 树，状态更新于虚拟 DOM 上，在内存中操作 JavaScript 对象比在浏览器上高效，将虚拟 DOM 映射为真实 DOM，再由浏览器渲染，通过 Diff 算法比较新旧虚拟 DOM 树中节点的差异并根据差异对真实 DOM 进行最小量更新。虚拟 DOM 抽象浏览器渲染过程，不再局限于浏览器 DOM，真正实现跨平台能力
