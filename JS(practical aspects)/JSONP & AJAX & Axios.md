```js
const jsonp = (url, params, callbackName) => {
  return new Promise((resolve, reject) => {
    const script = document.createElement("script");
    window[callbackName] = (data) => {
      resolve(data);
      document.body.removeChild(script);
    };
    params = { ...params, callback: callbackName };
    const arr = Object.keys(params).map((key) => `${key}=${params[key]}`);
    script.src = `${url}?${arr.join("&")}`;
    document.body.appendChild(script);
  });
};
```

```JavaScript
const ajax = (url, options) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    const { method, headers, data } = options;
    xhr.open(method, url, true); // 初始化请求，true 为异步执行
    for (const key in headers) xhr.setRequestHeader(key, headers[key]);
    if (method === "POST")
      xhr.setRequestHeader("Content-Type", "application/json");
    // 监听请求状态变化
    xhr.onreadystatechange = () => {
      if (xhr.readyState === 4) {
        if (200 <= xhr.status && xhr.status < 300) {
          try {
            const response = JSON.parse(xhr.responseText);
            return resolve(response);
          } catch (err) {
            return resolve(xhr.responseText);
          }
        } else {
          return reject(new Error(xhr.status));
        }
      }
    };
    xhr.onerror = () => reject(new Error("NetWork error"));
    xhr.ontimeout = () => reject(new Error("Request timeout"));
    xhr.send(data ? JSON.stringify(data) : null);
  });
};
```

```js
import axios from "axios";

const service = axios.create({ baseURL: "/api" });
let refreshTokenPromise = null;

service.interceptors.request.use((config) => {
  config.headers.Authorization = `Bearer ${localStorage.getItem("token")}`;
  return config;
});

service.interceptors.response.use(
  (res) => res,
  async (err) => {
    const { config, response } = err;
    if (response.status === 403 && !config._retry) {
      config._retry = true;
      refreshTokenPromise =
        refreshTokenPromise ||
        axios
          .post("/refreshToken", {
            refreshToken: localStorage.getItem("refreshToken"),
          })
          .then((res) => {
            localStorage.setItem("token", res.data.token);
            return res.data.token;
          })
          .finally(() => (refreshTokenPromise = null));
      const newToken = await refreshTokenPromise;
      config.headers.Authorization = `Bearer ${newToken}`;
      return service(config);
    }
    return Promise.reject(err);
  }
);

export default service;
```









