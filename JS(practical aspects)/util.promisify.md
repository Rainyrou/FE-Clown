`util.promisify` 将基于回调的函数转换为返回 Promise 的函数

```js
function promisify(fn) {
  if (typeof fn !== "function") throw new TypeError(`${fn} is not a function`);
  return function (...args) {
    return new Promise((resolve, reject) => {
	  // Node.js 标准回调格式 err + result
      function callback(err, ...results) {
        if (err) return reject(err);
        if (results.length === 1) return resolve(results[0]);
        return resolve(results);
      }
      args.push(callback);
      fn.call(this, ...args);
    });
  };
}

const fs = require("fs");
const readFileAsync = promisify(fs.readFile);
readFileAsync("./index.html", "utf8")
  .then((data) => console.log(data))
  .catch((err) => console.error(err));
```

