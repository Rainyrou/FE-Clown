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
