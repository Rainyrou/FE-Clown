Cross-Site Request Forgery：攻击者通过欺骗浏览器向一个已认证的网站发送请求，从而在未授权的情况下进行恶意操作。攻击者利用用户登录会话，从而绕过常规的身份验证步骤

###### 攻击流程

1. 用户在浏览器中登录目标网站，该网站为用户生成认证用的 cookie
2. 用户在没有登出目标网站的情况下，继续在同一浏览器中浏览其他网站
3. 用户不知情的情况下点击一个恶意链接或访问一个包含恶意代码的网站，这一恶意链接或代码向目标网站自动发送携带用户认证 Cookie 的请求
4. 若目标网站没有对 CSRF 攻击进行有效防御，其在接收到请求后认为是用户的自发操作，并执行相应请求

###### 防御措施

1. Anti-CSRF Token：服务端随机生成一个唯一 token，在用户会话中存储它，在执行敏感操作的请求中需包含该 token，服务端在执行操作前验证请求中的 token 与用户会话中存储的 token 是否一致

```HTML
<form id="dataForm" action="/submit-data" method="post">
  <input type="hidden" name="csrf_token" id="csrf_token">
  <input type="submit" value="Submit">
</form>

<script>
  fetch('/get-csrf-token')
    .then(response => response.json())
    .then(data => {
      document.getElementById('csrf_token').value = data.csrfToken;
    })
    .catch(error => {
      console.error('Error fetching CSRF token', error);
    });
</script>
```

2. 验证请求的来源：对 Cookies 设置 `SameSite` 属性，其限制第三方请求携带 Cookie，同时检查 HTTP 请求头的 `Referer` 和 `Origin` 字段，以确认请求是否来自合法的源

```JavaScript
document.cookie = "sessionId=your_session_id; SameSite=Strict";
```

3. 使用框架的内置防御如 Django 和 Spring Security
