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









