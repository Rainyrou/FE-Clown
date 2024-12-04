```js
Promise._race = (promises) => {
  return new Promise((resolve, reject) => {
    if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not an iterator`);
    promises = [...promises];
    promises.forEach((promise) =>
      Promise.resolve(promise).then(resolve).catch(reject)
    );
  });
};

Promise._race([
  new Promise((resolve, reject) => setTimeout(() => reject("A"), 1000)),
  new Promise((resolve, reject) => setTimeout(() => resolve("B"), 2000)),
  new Promise((resolve, reject) => setTimeout(() => resolve("C"), 3000)),
])
  .then((res) => console.log(res))
  .catch((err) => console.log(err)); // A
```
