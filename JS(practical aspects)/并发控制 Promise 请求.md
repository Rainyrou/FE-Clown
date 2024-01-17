通过并发控制来批量发送 Promise 请求，同时确保不会因为同时发送过多请求而导致性能问题

1. 分批处理：将所有 Promise 分成多个批次，每个批次包含固定数量的 Promise
2. 并行执行：每次并行处理一个批次的 Promise
3. 结果收集：将每个 Promise 的结果收集到一个数组中
4. 顺序执行：确保一个批次的 Promise 全部处理完毕后再处理下一个批次

###### 具体实现

```JavaScript
const promiseGenerator = (id) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(`Result of Promise ${id}`);
    }, 1000);
  });
};

const batchPromiseExecutor = (promises, batchSize) => {
  let results = [];
  let batchIndex = 0;

  const executeBatch = () => {
    return new Promise((resolve) => {
      const currentBatch = promises.slice(batchIndex, batchIndex + batchSize);
      batchIndex += batchSize;
      Promise.all(currentBatch).then((res) => {
        results = results.concat(res);
        resolve();
      });
    });
  };

  const executeAllBatches = () =>
    batchIndex < promises.length
      ? executeBatch().then(executeAllBatches)
      : Promise.resolve(results);
  return executeAllBatches();
};

const allPromises = [];
for (let i = 1; i <= 1000; ++i) allPromises.push(promiseGenerator(i));
batchPromiseExecutor(allPromises, 50).then((res) => console.log(res));
```

