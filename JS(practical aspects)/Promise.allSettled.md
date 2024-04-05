```js
Promise._allSettled = function (promises) {
  return new Promise((resolve, reject) => {
    if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not iterable`);
    promises = [...promises];
    if (promises.length === 0) resolve([]);
    const values = [];
    let count = 0;
    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then(
          (value) => (values[index] = { status: "fulfilled", value }),
          (reason) => (values[index] = { status: "rejected", reason })
        )
        .finally(() => {
          if (++count === promises.length) resolve(values);
        });
    });
  });
};
```

测试用例：

```js
Promise._allSettled([]).then((res) => console.log(res));
Promise._allSettled([Promise.resolve(1), Promise.resolve("a")]).then((res) =>
  console.log(res)
);
Promise._allSettled([
  Promise.resolve(1),
  Promise.reject(new Error("error")),
]).then((res) => console.log(res));
```

输出结果：

```js
[]
[
  { status: 'fulfilled', value: 1 },
  { status: 'fulfilled', value: 'a' }
]
[
  { status: 'fulfilled', value: 1 },
  {
    status: 'rejected',
    reason: Error: error
        at Object.<anonymous> (c:\Users\86158\Desktop\手写题\index.js:28:18)
        at Module._compile (node:internal/modules/cjs/loader:1105:14)
        at Object.Module._extensions..js (node:internal/modules/cjs/loader:1159:10)
        at Module.load (node:internal/modules/cjs/loader:981:32)
        at Function.Module._load (node:internal/modules/cjs/loader:822:12)
        at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:77:12)
        at node:internal/main/run_main_module:17:47
  }
]
```