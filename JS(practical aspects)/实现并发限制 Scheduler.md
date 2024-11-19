实现一个带并发限制的异步调度器，保证同时最多运行 2 个任务

```js
class Scheduler {
  constructor() {
    this.tasks = [];
    this.usingTask = [];
  }
  add(promiseCreator) {
    return new Promise((resolve, reject) => {
      promiseCreator.resolve = resolve;
      if (this.usingTask.length < 2) this.usingRun(promiseCreator);
      else this.tasks.push(promiseCreator);
    });
  }
  usingRun(promiseCreator) {
    this.usingTask.push(promiseCreator);
    promiseCreator().then(() => {
      promiseCreator.resolve();
      this.usingMove(promiseCreator);
      if (this.tasks.length > 0) this.usingRun(this.tasks.shift());
    });
  }
  usingMove(promiseCreator) {
    const index = this.usingTask.findIndex((item) => item === promiseCreator);
    this.usingTask.splice(index, 1);
  }
}

const timeout = (time) => new Promise((resolve) => setTimeout(resolve, time));
const scheduler = new Scheduler();
const addTask = (time, order) =>
  scheduler.add(() => timeout(time)).then(() => console.log(order));

addTask(4000, 4);
addTask(2000, 2);
addTask(3000, 3);
addTask(1000, 1);
```