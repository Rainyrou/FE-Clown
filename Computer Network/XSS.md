XSS（Cross-Site Scripting）攻击是一种安全威胁，攻击者利用 Web 网站的安全漏洞，将恶意的 HTML 标签或 JavaScript 代码注入到合法的 Web 页面中。当用户浏览这个被篡改的页面时，这些恶意脚本将在其浏览器中执行。这样，攻击者可以绕过浏览器的同源策略，获取用户的敏感数据，如 Cookie、Session ID 等

XSS 攻击大致可以分为三类：

1. 反射型 XSS：攻击代码出现在 URL 中，它是一个未经过滤的输入，当用户点击这个恶意链接时，服务端将这个输入返回给浏览器，之后在用户浏览器上执行
2. 存储型 XSS：攻击代码存储在服务端，如数据库、日志、评论。当其他用户浏览这个被篡改的页面时，恶意脚本将会执行
3. DOM 型 XSS：攻击者将攻击代码写入 DOM。攻击代码是在数据处理后传给 `innerHTML`（或其他函数）时执行的

###### 如何防御：

1. 数据验证、清洗和转义：

   - 永远不要信任用户输入，如果应用允许用户输入并显示 HTML 文本，确保使用可靠的的库或工具来对所有输入数据进行验证和清洗
   - 使用转义函数处理用户输入，确保这些数据不会被解释为代码。例如，当动态插入数据到 HTML、CSS、JavaScript、URLs 等时，都要进行转义
   - 使用支持自动转义的模板引擎

```JavaScript
<div>{userInput}</div> // React将自动转义
```

2. Content Security Policy (CSP)：

- 使用 CSP 指定哪些外部资源可以加载和执行，这可以通过 HTTP 头或 meta 标签实现

```HTML
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' https://trusted-source.com">
```

```HTTP
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-source.com
```

3. HttpOnly 和 Secure flags：

- 设置 cookie 时，使用 HttpOnly 防止 JavaScript 通过 `document.cookie` 获取 cookie，使用 Secure 确保 cookie 只通过加密的 HTTPS 传输

```Node
res.cookie('sessionId', 'SESSIONID123', { httpOnly: true, secure: true });
```

4. 避免使用 `eval()` 或其他可以将字符串作为代码执行的 JavaScript 函数，如 `new Function()`, `setTimeout()` 和 `setInterval()`

5. 定期更新应用和依赖库

6. 实施自动化的安全测试（如 XSS 扫描）和实时监控，以检测和预防 XSS 攻击
