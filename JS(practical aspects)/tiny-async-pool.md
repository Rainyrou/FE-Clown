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

`async` 和 `function*` 结合按需异步生成值，`async` 表示函数内部有异步操作，`function*` 表示该函数为生成器，其返回一个迭代器对象，生成器函数通过 `yield` 逐步返回值，而异步生成器函数则通过 `yield await` 等待异步操作完成后再逐步返回值

`iteratorFn` 的第二个参数保留对原始迭代器的访问，这是为了兼容访问整个上下文的复杂逻辑，使函数得以扩展以适应更多定制化需求，在部分任务执行逻辑中需查看所有输入项或依据其他项来决定当前任务的执行方式如基于整个输入数组的某些属性动态调整 `item` 的处理方式，`iteratorFn` 在某些依赖关系下选择性地跳过或重新处理某些输入项

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
