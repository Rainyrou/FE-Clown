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
const ajax = (url, options = {}) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    const { method = "GET", data, headers } = options;
    xhr.open(method, url, true);
    if (headers)
      for (const key in headers) xhr.setRequestHeader(key, headers[key]);
    xhr.onreadystatechange = () => {
      if (xhr.readyState === 4 && xhr.status >= 200 && xhr.status < 300) {
        let response;
        try {
          response = JSON.parse(xhr.responseText);
        } catch (err) {
          response = xhr.responseText;
        }
        resolve(response);
      } else {
        reject(new Error(`Request failed with status ${xhr.status}`));
      }
    };
    xhr.onerror = () => reject(new Error("NetWork error"));
    xhr.ontimeout = () => reject(new Error("Request timeout"));
    if (data && (method === "POST" || method === "PUT")) {
      xhr.setRequestHeader("Content-Type", "application/json");
      xhr.send(JSON.stringify(data));
    } else {
      xhr.send();
    }
  });
};
```









