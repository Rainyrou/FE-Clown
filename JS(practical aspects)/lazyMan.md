```js
LazyMan("Hank") 
// Hi! This is Hank!
LazyMan("Hank").sleep(10).eat("dinner") 
// Hi! This is Hank! 
// Wake up after 10 
// Eat dinner
LazyMan("Hank").eat("dinner").eat("supper") 
// Hi! This is Hank! 
// Eat dinner 
// Eat supper
LazyMan("Hank").sleepFirst(5).eat("supper") 
// Wake up after 5 
// Hi! This is Hank! 
// Eat supper
```

```js
class lazyMan {
  constructor(name) {
    this.tasks = [];
    this.name = name;
    this.sayHi();
    this.run();
  }
  sayHi() {
    this.tasks.push(() => console.log(`Hi! This is ${this.name}`));
    return this;
  }
  eat(name) {
    this.tasks.push(() => console.log(`Eat ${name}`));
    return this;
  }
  sleepFn(time) {
    return new Promise((resolve) => {
      setTimeout(() => {
        console.log(`Wake up after ${time}`);
        resolve();
      }, time * 1000);
    });
  }
  sleepFirst(time) {
    this.tasks.unshift(() => this.sleepFn(time));
    return this;
  }
  sleep(time) {
    this.tasks.push(() => this.sleepFn(time));
    return this;
  }
  run() {
    setTimeout(async () => {
      for (const task of this.tasks) await task();
    }, 0);
  }
}

const LazyMan = (name) => new lazyMan(name);
LazyMan("Hank").sleep(10).eat("dinner");
// Hi! This is Hank
// Wake up after 10
// Eat dinner
```