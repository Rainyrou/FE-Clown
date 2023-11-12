JSONP (JSON with Padding) 是一种跨域数据获取的方法。由于浏览器的同源策略，通常情况下，一个来自域 A 的网页不能直接访问域 B 的数据，但 `<script>` 标签是一个例外。因为在浏览器中，`<script>` 标签可以用来引入跨域的脚本，并执行其中的 JavaScript 代码。基于这一特点，JSONP 应运而生。它并不是一种新的技术，而是对已有技术的一种巧妙的应用

其基本原理如下：
1. 网页中定义一个回调函数
2. 通过 `<script>` 标签向服务端请求数据，同时传递这个回调函数的名称
3. 服务器将数据与该回调函数名组合成一个 JavaScript 语句，并返回
4. 当脚本从服务器加载完毕后，浏览器会立即执行这个语句，从而实现了跨域数据的获取

虽然 JSONP 提供了一种解决跨域问题的方法，但它有安全风险，因为它完全信任服务器返回的数据。如果服务器返回的是恶意代码，那么这段代码将在用户的浏览器中被执行。现代的跨域请求方法，如 CORS，已使得 JSONP 逐渐被淘汰

```JavaScript
const _myjsonp = (url, params, callbackName) => {
    return new Promise((resolve, reject) => {
	    // 创建一个新的`<script>`标签
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

* 返回一个 Promise 意味着一旦 JSONP 请求的数据可用，就可以轻松地将其传递给调用者，同时还可以在发生错误时进行适当处理

* 在全局对象 `window` 上定义了一个回调函数。服务器将返回一个调用这个函数的脚本，并传递需要的数据作为参数。当这个函数被调用时，Promise 将被解析，这些数据作为其解析值（函数的调用者可以使用 `.then` 来获取并处理这些数据），且 `<script>` 标签将被从文档中删除（这是为了保持文档的整洁并避免任何潜在的内存泄露）

* 首先，将 `callbackName` 添加到查询参数对象中，在JSONP中，请求的URL通常需要一个 `callback` 参数来告诉服务器应该返回什么名字的回调函数。

* `Object.keys(parameters)` 获取 `parameters` 对象的所有键名，并返回一个键名数组。`map` 函数遍历这个键名数组，并为每个键名返回一个字符串，这个字符串的格式是 `key=value`，其中 `key` 是参数的键名，`value` 是参数的值。最终返回一个字符串数组，每个字符串都是一个查询参数

* 设置 `<script>` 标签的 `src` 属性，这样它就会从给定的 URL 加载脚本。查询字符串被添加到 URL 后面，将查询参数传递给服务器。例如，如果我们的 `parameters` 是 `{ name: "Alice", age: 30, callback: "handleResponse" }`，那么最终的URL可能是 `http://example.com?name=Alice&age=30&callback=handleResponse`

* 一旦 `<script>` 标签被添加到文档中，就会触发浏览器发起对指定URL的请求。由于 `<script>` 元素是异步加载的，代码不会停下来等待脚本加载完成，而是继续执行。当脚本加载完成并执行时，预期的回调函数将被调用，处理返回的数据
