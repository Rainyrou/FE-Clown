实现一个并发限制的异步调度器，保证同时最多执行 2 个任务

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

* `add` 方法返回新的 Promise (P)，其接收 `resolve` 参数，`promiseCreator.resolve = resolve` 将 Promise (P) 的 `resolve` 函数挂载至 `promiseCreator` 函数对象上，如此一来，一旦 `promiseCreator` 执行且完成时，我们通过其 `resolve` 控制外部 Promise (P) 的状态，`promiseCreator.resolve()` 通知 `addTask` 的调用者任务已完成，要知道 `console.log` 只有在 Promise (P) 被 resolved 时才执行

尽可能以 max 并发数并发请求且按照顺序返回结果

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
