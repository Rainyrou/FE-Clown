Cross-Site Scripting：攻击者利用网站的安全漏洞，将恶意的 HTML 或 JavaScript 注入到合法的页面中，当用户浏览这个被篡改的页面时，这些恶意的 HTML 或 JavaScript 将被执行，攻击者绕过浏览器的同源策略，获取用户的敏感数据

1. 反射型 XSS：攻击代码出现在 URL 中，其为一个未经过滤的输入，当用户点击该恶意链接时，服务端将其返回给浏览器，在用户浏览器上执行
2. 存储型 XSS：攻击代码存储在服务端如数据库、日志，当其他用户浏览这个被篡改的页面时，这些恶意代码将被执行
3. DOM 型 XSS：攻击者将攻击代码写入 DOM，这些恶意代码是在数据处理后传给 `innerHTML` 或其他函数时执行的

###### 如何防御：

1. 数据验证、清洗和转义：

   - 永远不要信任用户输入，若允许用户输入并显示 HTML 文本，确保使用可靠的的库或工具来对所有输入数据进行验证和清洗
   - 使用转义函数处理用户输入，确保它们不会被解释为代码，动态插入数据到 HTML、CSS、JavaScript、URLs 需进行转义
   - 使用支持自动转义的模板引擎

```JavaScript
<div>{userInput}</div> // React将自动转义
```

2. CSP：使用 Content Security Policy 指定哪些外部资源可加载和执行，通过 HTTP 头部或 HTML meta 标签实现

```HTML
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' https://trusted-source.com">
```

```HTTP
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-source.com
```

3. HttpOnly 和 Secure flags：设置 Cookie 时，HttpOnly 可防止 JavaScript 通过 `document.cookie` 获取 Cookie，Secure 可确保 Cookie 只通过 HTTPS 传输

```Node
res.cookie('sessionId', 'SESSIONID123', { httpOnly: true, secure: true });
```

4. 避免使用 `eval` 或其他可将字符串作为代码执行的 JavaScript 函数如 `new Function`, `setTimeout` 和 `setInterval`
5. 定期更新
6. 实施自动化安全测试和实时监控
