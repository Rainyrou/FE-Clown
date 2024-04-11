分批处理，单个批次含固定数量的 Promise，每次并行单个批次的 Promise，将单个 Promise 的结果收集到一个数组中，依照顺序执行，单个批次的 Promise 全部执行完后再执行下一批次

```JavaScript
const promiseGenerator = (id) =>
  new Promise((resolve) =>
    setTimeout(() => resolve(`Result of Promise ${id}`), 1000)
  );

const batchPromiseExecutor = (promises, batchSize) => {
  let results = [],
    batchIndex = 0;
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

