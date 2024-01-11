axios 是一个基于 Promise 的 HTTP 客户端，用于浏览器和 node.js 中。其拦截器基于 AOP（面向切面编程）的理念。它允许你在函数执行的前后进行预处理或自定义操作，而不必修改函数本身，这使得代码易于维护和复用

###### Request Interceptors

请求拦截器允许你在发送请求之前对请求配置进行修改或执行某些操作

```JavaScript
axios.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem("token");
    if (token) config.headers.Authorization = `bear ${token}`;
    return config;
  },
  (error) => Promise.reject(error)
);
```

###### Response Interceptors

响应拦截器允许你在服务器的响应返回到客户端之前对它们进行处理

```JavaScript
axios.interceptors.response.use(
  (response) => {
    return response;
  },
  (error) => {
    if (error.response && error.response.status === 401)
      console.log("Fail, please login again");
    return Promise.reject(error);
  }
);
```

###### 移除拦截器

```JavaScript
const myInterceptor = axios.interceptors.request.use(...);
axios.interceptors.request.eject(myInterceptor);
```
