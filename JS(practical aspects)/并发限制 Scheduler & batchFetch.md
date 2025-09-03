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
