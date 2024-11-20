将一个遵循 Node.js 回调风格即 `callback(error, result)` 的函数转换为返回 Promise 的函数

```js
const promisify = (fn, ctx) => {
  return function (...args) {
    const context = ctx || this;
    return Promise((resolve, reject) => {
      // 调用原始函数，将最后一个参数作为回调
      fn.call(context, ...args, function (...callbackArgs) {
        // 单独取出回调函数中的第一个参数
        const [err, ...result] = callbackArgs;
        return err
          ? reject(err)
          : resolve(result.length > 1 ? result : result[0]);
      });
    });
  };
};
```

```js
function asyncTask(arg1, arg2, callback) {
  setTimeout(() => {
    if (arg1 === "error") callback(new Error("Something went wrong"), null);
    else callback(null, `Result: ${arg1} ${arg2}`);
  }, 1000);
}

const promisifiedTask = promisify(asyncTask);
promisifiedTask("Hello", "World")
  .then((result) => console.log(result)) // "Result: Hello World"
  .catch((err) => console.error(err.message));
```