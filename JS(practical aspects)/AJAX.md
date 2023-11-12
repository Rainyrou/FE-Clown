```JavaScript
function ajax(url, options = {}) {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();
        const { method = 'GET', data, headers } = options;

		// 初始化请求
		// 参数分别为请求 method、请求 URL、异步标识、账号和密码
        xhr.open(method, url, true);

		// 设置请求头
        if(headers) {
            for(let key in headers) {
	            // 参数分别为属性的名称和相应的值
                xhr.setRequestHeader(key, headers[key]);
            }
        }

		// 请求状态的处理
		// `onreadystatechange` 事件会在 `readyState` 属性发生变化时触发
        xhr.onreadystatechange = () => {
	        // 当 `readyState` 值为4时，表示请求已完成
            if(xhr.readyState === 4) {
                if(xhr.status >= 200 && xhr.status < 300) {
                    let response;
                    try {
	                    // 解析 JSON 字符串，返回类型为 Object
                        response = JSON.parse(xhr.responseText);
                    } catch(err) {
                        response = xhr.responseText;
                    }
                    resolve(response);
                } else {
                    reject(new Error(`Request failed with status ${xhr.status}`));
                }
            }
        };

		// 错误处理
        xhr.onerror = () => reject(new Error('Network error'));
        xhr.ontimeout = () => reject(new Error('Request time out'));

		// 发送请求
        if(data && (method === 'POST' || method === 'PUT')) {
            xhr.setRequestHeader('Content-Type', 'application/json');
            xhr.send(JSON.stringify(data));
        } else {
            xhr.send();
        }
    })
}
```









