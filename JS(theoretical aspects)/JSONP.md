`<script>` 标签在浏览器中可进行跨域，定义一个回调函数，通过 `<script>` 标签向服务端请求，传递该回调函数，服务端返回数据 + 回调函数组合成的JS语句，浏览器执行该语句，实现跨域

```JavaScript
const _myjsonp = (url, params, callbackName) => {
    return new Promise((resolve, reject) => {
        const script = document.createElement('script');
        window[callbackName] = data => {
            resolve(data);
            document.body.removeChild(script);
        }
        params = { ...params, callback: callbackName };
        const arr = Object.keys(params).map(key => `${key}=${params[key]}`);
        script.src = `${url}?${arr.join('&')}`;
        document.body.appendChild(script);
    })
};
```
