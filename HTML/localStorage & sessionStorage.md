localStorage 和 sessionStorage 都是 Web 存储的两种形式，它们允许我们在用户的浏览器上存储键-值对。这两种存储方式与 cookies 相似，但它们的容量更大，且对于大多数应用场景来说，API 使用起来更简单

localStorage 数据持久化本地存储（除非主动删除），即使浏览器关闭和重新打开也不会消失。sessionStorage 数据只在一个会话期间存储

- `localStorage` 是一个只读属性，你不能为它赋予一个新值，但你可以访问和修改其内容
* Document 源（origin）是指一个特定的组合：协议、域名以及端口。`localStorage` 是特定于 origin 的，不同的 origin 之间不能互相访问各自的 `localStorage` 数据
* `localStorage` 的键值对总是以字符串的形式存储
* 从底层来看，`localStorage` 和 `sessionStorage` 都是浏览器提供的 Web Storage API 的一部分。这些存储在客户端的 Web 存储方案为网页提供了比传统的 cookies 更为强大、更安全的存储能力

###### 相同点

- 都与 HTTP 无关，是 HTML5 提供的标准，当发起 HTTP 请求时不会像 Cookie 一样自动携带
- 存储类型都是 String 类型
- 通常为每个域名分配 5-10MB 的空间（取决于浏览器）
- 作用域仅限于文档源（同源策略）

###### 底层原理

1. Web 存储使用的是浏览器提供的 API。数据实际上存储在浏览器的沙箱环境中，不同浏览器实现方式可能不同，但通常它们会在用户的硬盘上为每个域分配一个存储区域
2. 与 cookie 不同，Web 存储数据不会在每次 HTTP 请求中发送到服务器，这有助于减少数据传输的开销
3. 尽管数据存储在用户的浏览器上，但切忌存储敏感信息，因为它们对任何可访问该浏览器的人来说都是可见的。此外由于它遵循同源策略，不同源的脚本无法访问其他源的数据
4. localStorage 和 sessionStorage 都是同步 API，这意味着它们可能会阻止主线程的其他操作，尤其是在进行大量读写操作时。因此最好只在需要时使用，并避免在主线程关键路径上进行大量操作

###### 常用操作

- 储存数据

```JavaScript
localStorage.setItem('key', 'value');
sessionStorage.setItem('key', 'value');
```

- 获取数据

```JavaScript
localStorage.getItem('key');
sessionStorage.getItem('key');
```

- 删除数据

```JavaScript
localStorage.removeItem('key');
sessionStorage.removeItem('key');
```

- 清空数据

```JavaScript
localStorage.clear();
sessionStorage.clear();
```
