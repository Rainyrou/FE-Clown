###### 服务端设置

1. `Set-Cookie`: 服务端响应时设置 `Set-Cookie` 字段以包含 Cookie，配置以确保浏览器在跨域情况下设置 Cookie
2. `Access-Control-Allow-Credentials`: 服务端响应时设置 `Access-Control-Allow-Credentials` 字段，允许浏览器在发送跨源请求时携带凭据如 Cookie
3. `Access-Control-Allow-Origin`: 不能设置为 `*`，浏览器检查 `Origin` 与 `Access-Control-Allow-Origin` 是否匹配
4. 预检请求需设置 `Access-Control-Allow-Headers` 和 `Access-Control-Allow-Methods`
5. `Secure`: 只有在 HTTPS 时浏览器才携带 Cookie
6. `SameSite=None`: 表示 Cookie 在任何情况下均被发送
7. HttpOnly: 防止 JavaScript 通过 `document.cookie` 获取 Cookie

```HTTP
Set-Cookie: key=value; Path=/; Domain=.example.com; Secure; HttpOnly; SameSite=None
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: https://www.your-website.com
```

###### 客户端设置

1. `withCredentials`：浏览器在发送跨源请求时携带凭据如 Cookie
2. `Origin`
3. `Content-Type`: 简单请求 -> `application/x-www-form-urlencoded`、`multipart/form-data` 或 `text/plain`

```JavaScript
fetch('https://api.example.com/data', {
  method: 'GET',
  credentials: 'include'
})
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error('Error:', error));
```
