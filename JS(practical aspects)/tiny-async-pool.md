[async-pool/lib/es9.js at master · rxaviers/async-pool · GitHub](https://github.com/rxaviers/async-pool/blob/master/lib/es9.js)

```js
async function* asyncPool(concurrency, iterator, iteratorFn) {
  const executing = new Set();
  async function consume() {
    const [promise, value] = await Promise.race(executing);
    executing.delete(promise);
    return value;
  }
  for (const item of iterator) {
    const promise = (async () => await iteratorFn(item, iterator))().then(
      (value) => [promise, value]
    );
    executing.add(promise);
    if (executing.size >= concurrency) yield await consume();
  }
  while (executing.size) yield await consume();
}
```

测试用例：

```js
const timeout = (time) =>
  new Promise((resolve) => setTimeout(() => resolve(time), time));
for await (const ms of asyncPool(2, [1000, 5000, 3000, 2000], timeout))
  console.log(ms);
```