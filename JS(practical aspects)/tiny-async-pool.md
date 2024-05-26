[async-pool/lib/es9.js at master · rxaviers/async-pool · GitHub](https://github.com/rxaviers/async-pool/blob/master/lib/es9.js)

```js
async function* asyncPool(concurrency, iterator, iteratorFn) {
  const executing = new Set();
  async function consume() {
    // `Promise` 对象被解析为一个数组即 `Promise` 对象本身和 `iteratorFn` 执行结果
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
async function update(machine) {
  return await machine.update();
}

// 假设的 machines 数组，每个 machine 对象有一个 update 方法
const machines = [
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 1 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 2 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 3 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 4 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 5 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 6 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 7 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 8 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 9 updated";
    },
  },
  {
    update: async () => {
      await new Promise((r) => setTimeout(r, 1000));
      return "Machine 10 updated";
    },
  },
];

(async () => {
  for await (const result of asyncPool(3, machines, update)) {
    console.log(result);
  }
})();
```