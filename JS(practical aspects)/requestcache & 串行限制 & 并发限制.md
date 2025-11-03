实现 requestcache 函数，若为相同 URL 则直接返回缓存，超过限制则使用 LRU策略

```js
class RequestCache {
  constructor(capacity) {
    this.map = new Map();
    this.capacity = capacity;
  }

  request(url) {
    if (this.map.has(url)) {
      const promise = this.map.get(url);
      this.map.delete(url);
      this.map.set(url, promise);
      return promise;
    }
    const promise = (async () => {
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error(response.status);
        const result = await response.json();
        return result;
      } catch (err) {
        throw new Error(err);
      }
    })();
    this.map.set(url, promise);
    if (this.map.size > this.capacity)
      this.map.delete(this.map.keys().next().value);
    return promise;
  }
}
```

###### 串行限制

变式一：

```js
const serialFetch = async (urls) => {
  const ans = [];
  for (const url of urls) {
    const response = await fetch(url);
    if (!response.ok) throw new Error(response.status);
    const data = await response.json();
    ans.push(data);
  }
  return ans;
};

const urls = [
  "https://jsonplaceholder.typicode.com/posts/1",
  "https://jsonplaceholder.typicode.com/posts/2",
  "https://jsonplaceholder.typicode.com/invalid-url",
  "https://jsonplaceholder.typicode.com/posts/3",
];

serialFetch(urls)
  .then((res) => console.log(res))
  .catch((err) => console.error(err));
```

变式二：

```js
function createPipeline(...tasks) {
  return async function (initialVal) {
    let result = initialVal;
    for (const task of tasks) result = await task(result);
    return result;
  };
}

const addOne = (x) => x + 1;
const asyncMultiplyTwo = (x) =>
  new Promise((resolve) => setTimeout(() => resolve(x * 2), 500));
const subtractThree = (x) => x - 3;
const pipeline = createPipeline(addOne, asyncMultiplyTwo, subtractThree);
pipeline(5)
  .then((res) => console.log(res)) // 9
  .catch((err) => console.error(err));
```

变式三：

```js
const mergePromise = (promisesFn) => {
  let sequence = Promise.resolve();
  const ans = [];
  promisesFn.forEach(
    (promiseFn) =>
      (sequence = sequence.then(() => promiseFn()).then((res) => ans.push(res)))
  );
  return sequence.then(() => ans);
};

const timeout = (time) =>
  new Promise((resolve) => setTimeout(() => resolve(), time));
const promise1 = () => timeout(2000).then(() => "promise1");
const promise2 = () => timeout(1000).then(() => "promise2");
const promise3 = () => timeout(3000).then(() => "promise3");
mergePromise([promise1, promise2, promise3]).then((res) => console.log(res));
```

###### 并发限制

实现并发限制的异步调度器，保证同时最多执行 2 个任务：

`add` 方法返回新的 Promise (P)，其接收 `resolve` 参数，`promiseCreator.resolve = resolve` 将 Promise (P) 的 `resolve` 函数挂载至 `promiseCreator` 函数对象上，如此一来，一旦 `promiseCreator` 执行且完成时，我们通过其 `resolve` 控制外部 Promise (P) 的状态，`promiseCreator.resolve()` 通知 `addTask` 的调用者任务已完成，要知道 `console.log` 只有在 Promise (P) 被 resolved 时才执行

```js
class Scheduler {
  constructor() {
    this.tasks = [];
    this.usingTasks = [];
  }
  add(promiseCreator) {
    return new Promise((resolve, reject) => {
      promiseCreator.resolve = resolve;
      if (this.usingTasks.length < 2) this.usingRun(promiseCreator);
      else this.tasks.push(promiseCreator);
    });
  }
  usingRun(promiseCreator) {
    this.usingTasks.push(promiseCreator);
    promiseCreator().then(() => {
      promiseCreator.resolve(); 
      this.usingMove(promiseCreator);
      if (this.tasks.length) this.usingRun(this.tasks.shift());
    });
  }
  usingMove(promiseCreator) {
    const index = this.usingTasks.findIndex((item) => item === promiseCreator);
    this.usingTasks.splice(index, 1);
  }
}

const timeout = (time) => new Promise((resolve) => setTimeout(() => resolve(), time));
const scheduler = new Scheduler();
const addTask = (time, order) =>
  scheduler.add(() => timeout(time)).then(() => console.log(order));

addTask(4000, 4);
addTask(2000, 2);
addTask(3000, 3);
addTask(1000, 1); // 2 4 3 1
```

尽可能以 max 并发数并发请求且按照顺序返回结果：

```js
const batchFetch = (urls, max) => {
  const results = new Array(urls.length).fill(null);
  const tasks = urls.map((url, index) => ({ url, index }));

  const runner = async () => {
    while (tasks.length) {
      const { url, index } = tasks.shift();
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error(response.status);
        const data = await response.json();
        results[index] = data;
      } catch (error) {
        results[index] = error;
      }
    }
  };

  const runners = new Array(max).fill(null).map(() => runner());
  return Promise.all(runners).then(() => results);
};
```
