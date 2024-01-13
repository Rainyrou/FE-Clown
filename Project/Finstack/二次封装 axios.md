>[!todo] To do 
>全文背诵

##### 全局默认配置

```JavaScript
const MODE = import.meta.env.MODE;

axios.defaults.baseURL = MODE === "development" ? "/api" : "";
axios.defaults.withCredentials = true;
axios.defaults.headers["X-Request-With"] = "XHttpRequest";
axios.defaults.headers["Authorization"] = `${
  localStorage.getItem("token") || null
}`;
axios.defaults.headers.port["Content-Type"] = "application/json";
```

axios 官方文档，全局默认配置，其对于所有 axios 请求均生效，除非在单个请求中明确地覆盖它们。设置这些默认值可以确保你不必在每个请求中重复设置相同的参数

###### baseURL

- 环境变量用于判断当前的运行环境是开发 or 生产环境
- baseURL 字如其名

###### withCredentials

- 当我们使用 axios 发送请求时，默认携带跨站点的身份认证信息，如 cookies 和 HTTP Basic authentication 信息
- 在跨域请求中，若不设置这个参数，cookies 和 auth 信息不会被包含在请求中（这是出于安全原因）。但有时我们需要与另一个域名进行身份验证，这时就需要开启 `withCredentials`

###### XMLHttpRequest

- 使用此头来识别这是一个通过 XMLHttpRequest 发出的 AJAX 请求

###### Authorization

```JavaScript
axios.defaults.headers['Authorization'] = `${localStorage.getItem('token') || null}`;
```

- 默认请求头 `Authorization` 的值是从浏览器的 localStorage 中获取的
- 当使用 token-based authentication (如 JWT) 时，我们将 token 存储在 localStorage。然后在每个请求中携带此 token，告诉服务器我们已登录

###### Content-Type

- 为 POST 请求设置默认的 `Content-Type` 头为 `'application/json'`

##### axios 拦截器

```JavaScript
axios.interceptors.response.use(res => {
    if (typeof res.data !== 'object') {
        Toast.show('服务端异常！')
        return Promise.reject(res)
    }
    if (res.data.code !== 200) {
        if (res.data.msg) Toast.show(res.data.msg)

        /**
         * 没有登录的用户，默认跳到 /login 页面
         */

        if (res.data.code === 401) {
            window.location.href = '/login'
        }
        return Promise.reject(res.data)
    }

    return res.data
})
```

- 在用户未认证或认证失败的情况下自动重定向到登录页面
- 当 `window.location.href` 的值改变时，浏览器的内部导航机制会被触发，浏览器会接收到这个新值，并开始导航到这个新的 URL。这背后的原理与你在地址栏中输入一个新的 URL 并按回车键是一样的
