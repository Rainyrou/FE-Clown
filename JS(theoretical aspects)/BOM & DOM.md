Browser Object Model：用于连接 JavaScript 与浏览器窗口，JavaScript 据此控制浏览器行为

`window`：JavaScript 在浏览器中的全局对象（Node.js 中为 `global`）和浏览器窗口对象，全局对象即对浏览器运行环境的抽象，其管理全局命名空间并提供对全局变量和函数的访问，浏览器窗口对象包含 `location`、`document`、`history`、`navigator`、`console`、`screen`、`innerWidth`、`parent`、`top`、`frames` 和 `localStorage` 等属性，`hashchange`、`load`、`resize`、`focus`、`blur`、`online` 和 `error` 等事件，`alert`、`confirm`、`prompt`、`open`、`close`、`setTimeout`、`setInterval`、`scrollTo`、`addEventListener` 和 `dispatchEvent` 等。综上，`window` 的属性、事件和方法并非全部源于自身，而有相当部分来自继承，`window` 为实例对象，由 `Window` 类构造而来，而 `Window` 类继承 `EventTarget` 类，因此 `window`  对象不仅有 `Window` 类般操作浏览器窗口的功能，还有 `EventTarget` 类般的事件处理能力

```
window 对象 -> Window 类 -> EventTarget 类
```

`document`：当前浏览器窗口对应的文档对象如 HTML 和 XML
`location`：浏览器所连接的 URL 信息

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

- `reload`：重新加载当前页面
- `replace(url)`：导航至新 URL，不保存当前页面，无法通过后退按钮返回
- `assign(url)`：导航至新 URL，保存当前页面，可通过后退按钮返回

`history`：操作浏览器的历史记录如前进和后退

属性：

`state`：当前保存的会话状态
`length`：会话记录条数

方法：

`forward`：前进即 `history.go(1)`
`back`：返回即 `history.go(-1)`
`go`：导航至某页
`pushState`：在不重新加载页面的情况下导航至指定 URL，添加新的历史会话于浏览器的历史记录栈
`replaceState`：在不重新加载页面的情况下导航至新 URL，替换当前历史会话

旧版页面应用页面跳转或变化时通过重新加载实现，浏览器自动加载会话记录于历史记录栈，而随着 SPA 推广，我们可在不重新加载页面的情况下实现 URL 变化，用户通过浏览器前进后退按钮进行导航
