实现 requestcache 函数，若为相同 URL 则直接返回缓存，超过限制则使用 LRU 策略

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
    let ans = initialVal;
    for (const task of tasks) ans = await task(ans);
    return ans;
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
const mergePromise = async (promisesFn) => {
  const ans = [];
  for (const promiseFn of promisesFn) {
    const result = await promiseFn();
    ans.push(result);
  }
  return ans;
};

const timeout = (time) => new Promise((resolve) => setTimeout(resolve, time));
const promise1 = () => timeout(2000).then(() => "promise1");
const promise2 = () => timeout(1000).then(() => "promise2");
const promise3 = () => timeout(3000).then(() => "promise3");
mergePromise([promise1, promise2, promise3]).then((res) => console.log(res));
```

###### 并发限制

实现并发限制的异步调度器，保证同时最多执行 2 个任务：

`promiseCreator.resolve = resolve` 将 Promise (P) 的 `resolve` 函数挂载至 `promiseCreator` 函数对象上，当 `promiseCreator` 执行完成时，通过 `resolve` 控制外部 Promise (P) 的状态

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

const timeout = (time) => new Promise((resolve) => setTimeout(resolve, time));
const scheduler = new Scheduler();
const addTask = (time, order) =>
  scheduler.add(() => timeout(time)).then(() => console.log(order));

addTask(4000, 4);
addTask(2000, 2);
addTask(3000, 3);
addTask(1000, 1); // 2 4 3 1
```

尽可能以 max 并发数并发请求且根据顺序返回结果：

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

基于 requestIdleCallback 的时间切片调度器：

- `deadline.timeRemaining`：返回当前帧剩余的空闲时间
- `deadline.didTimeout`：判断 `requestIdleCallback` 的回调是因为浏览器有空闲时间（true）还是因为超时（false）而触发

```js
const tasks = [];
let isRun = false;

const scheduler = (task) => {
  tasks.push(task);
  if (!isRun) {
    isRun = true;
    requestIdleCallback(execute);
  }
};

const execute = (deadline) => {
  while (
    (deadline.timeRemaining() > 0 || deadline.didTimeout) &&
    tasks.length > 0
  ) {
    const curTask = tasks.shift();
    curTask();
  }
  if (tasks.length > 0) requestIdleCallback(execute);
  else isRun = false;
};

const createTask = (i) => () => console.log(i);

const run = () => {
  for (let i = 0; i < 100000; ++i) scheduler(createTask(i));
};

run();
```