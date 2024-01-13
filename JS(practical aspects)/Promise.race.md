```JavaScript
Promise._race = function (promises) {
  return new Promise((resolve, reject) => {
    if (promises === null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not an iterator`);
    promises = [...promises];
    for (const p of promises) Promise.resolve(p).then(resolve).catch(reject);
  });
};
```

由于只要有一个 Promise 完成，`resolve` 或 `reject` 就会被调用，整个 `Promise._race` 就会完成。而其他还未完成的 Promise 仍会继续执行，但它们的结果不会影响最终结果

测试用例：

```JavaScript
Promise._race([
  new Promise((_, reject) => setTimeout(() => reject("A"), 100)),
  new Promise((resolve) => setTimeout(() => resolve("B"), 200)),
  new Promise((resolve) => setTimeout(() => resolve("C"), 300)),
]).catch((reason) => {
  console.log(reason); // A
});
```
