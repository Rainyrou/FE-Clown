```js
const _jsonp = (url, params, callbackName) => {
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