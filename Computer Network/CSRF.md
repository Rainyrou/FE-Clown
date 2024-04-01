CSRF（Cross-Site Request Forgery，跨站请求伪造）：攻击者通过欺骗用户浏览器去向一个已认证过的网站发送请求，从而在未授权的情况下进行恶意操作。攻击者利用用户的登录会话，从而绕过了常规的身份验证步骤

###### 攻击流程

1. 用户在浏览器中登录目标网站，该网站为用户生成了认证用的 cookie
2. 用户在没有登出目标网站的情况下，继续在同一浏览器中浏览其他网站
3. 用户不知情的情况下点击了一个恶意链接，或访问了一个包含恶意代码的网站，这一恶意链接或恶意代码自动向目标网站发送携带用户认证 cookie 的请求
4. 如果目标网站没有对 CSRF 攻击进行有效防御，该网站在收到请求后会认为是用户自发的操作，并执行相应的请求

###### 防御措施

1. Anti-CSRF Token

- 服务器随机生成一个唯一的 token，并在用户会话中存储它
- 在每次进行敏感操作的请求中必须包含这个 token，服务器在执行操作前验证请求中的 token 与用户会话中存储的 token 是否一致

```HTML
<form id="dataForm" action="/submit-data" method="post">
  <input type="hidden" name="csrf_token" id="csrf_token">
  <input type="submit" value="Submit">
</form>

<script>
  // 假设后端已将 CSRF token 设置在用户会话中，且可以通过某个 API 接口获取
  fetch('/get-csrf-token')
    .then(response => response.json())
    .then(data => {
      // 将 token 设置为隐藏字段的值
      document.getElementById('csrf_token').value = data.csrfToken;
    })
    .catch(error => {
      console.error('Error fetching CSRF token', error);
    });
</script>
```

2. 验证请求的来源

- 对 cookies 设置 `SameSite` 属性，该属性限制第三方请求携带 cookie

```JavaScript
document.cookie = "sessionId=your_session_id; SameSite=Strict";
```

- 检查 HTTP 请求头中的 `Referer` 和 `Origin` 字段，以确认请求是否来自合法的源

3. 使用框架的内置防御

许多现代 Web 开发框架都内置了 CSRF 防御措施，例如 Django 和 Spring Security
