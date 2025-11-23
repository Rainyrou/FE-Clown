单例模式：

```js
class Singleton {
  constructor(name, age) {
    if (!Singleton.instance) {
      this.name = name;
      this.age = age;
      Singleton.instance = this;
    }
    return Singleton.instance;
  }
}

const s1 = new Singleton("Clown", 21);
const s2 = new Singleton("Rain", 19);
console.log("s1", s1); // s1 Singleton { name: 'Clown', age: 21 }
console.log("s2", s2); // s1 Singleton { name: 'Clown', age: 21 }
```

观察者模式：

```JavaScript
class Subject {
  constructor() {
    this.observers = [];
  }
  add(observer) {
    this.observers.push(observer);
  }
  delete(observer) {
    const index = this.observers.indexOf(observer);
    if (index !== -1) this.observers.splice(index, 1);
  }
  notify(data) {
    this.observers.forEach((observer) => observer.update(data));
  }
}

class Observer {
  update(data) {
    console.log(`receive data: ${data}`);
  }
}

const subject = new Subject();
const observer1 = new Observer();
const observer2 = new Observer();

subject.add(observer1);
subject.add(observer2);
subject.notify("Hello world!");
subject.delete(observer1);
subject.notify("Goodbye world!");

// receive data: Hello world!
// receive data: Hello world!
// receive data: Goodbye world!
```

EventBus：

```js
class EventBus {
  constructor() {
    this.eventMap = {};
  }
  on(eventName, callback) {
    if (!this.eventMap[eventName]) this.eventMap[eventName] = [];
    this.eventMap[eventName].push(callback);
  }
  once(eventName, callback) {
    const onceCallback = (...args) => {
      callback(...args);
      this.off(eventName, onceCallback);
    };
    this.on(eventName, onceCallback);
  }
  emit(eventName, ...args) {
    this.eventMap[eventName].forEach((callback) => callback(...args));
  }
  off(eventName, callback) {
    this.eventMap[eventName] = this.eventMap[eventName].filter(
      (item) => item !== callback
    );
  }
}

const emitter = new EventBus();
const onFoo = (e) => console.log("foo（可多次触发）:", e);
const onOnceBar = (e) => console.log("bar（仅触发一次）:", e);

emitter.on("test", onFoo);
emitter.once("test", onOnceBar);
emitter.emit("test", { data: "first" });
emitter.emit("test", { data: "second" });
// foo（可多次触发）: { data: "first" }
// bar（仅触发一次）: { data: "first" }
// foo（可多次触发）: { data: "second" }
```

```JavaScript
class EventBus {
  constructor(maxListeners) {
    this.eventMap = {};
    this.maxListeners = maxListeners || Infinity;
    this.callbackId = 0;
  }
  subscribe(eventName, callback) {
    if (!Reflect.has(this.eventMap, eventName))
      Reflect.set(this.eventMap, eventName, {});
    if (
      this.maxListeners !== Infinity &&
      Object.keys(this.eventMap[eventName]).length >= this.maxListeners
    ) {
      console.warn(`the event ${eventName} has exceeded the max listeners`);
      return;
    }
    const thisCallbackId = this.callbackId++;
    this.eventMap[eventName][thisCallbackId] = callback;
    const unSubscribe = () => {
      delete this.eventMap[eventName][thisCallbackId];
      if (Object.keys(this.eventMap[eventName]).length === 0)
        delete this.eventMap[eventName];
    };
    return { unSubscribe };
  }
  emit(eventName, ...args) {
    if (!Reflect.has(this.eventMap, eventName)) {
      console.warn(`never subscribe the event ${eventName}`);
      return;
    }
    const callbackList = this.eventMap[eventName];
    if (Object.keys(callbackList).length === 0) {
      console.warn(`the event ${eventName} has no function to execute`);
      return;
    }
    for (const callback of Object.values(callbackList))
      callback.call(this, ...args);
  }
  clear(eventName) {
    if (!eventName) {
      console.warn(`please provide the event name to be cleared`);
      return;
    }
    delete this.eventMap[eventName];
  }
  clearAll() {
    this.eventMap = {};
  }
}
const eventBus = new EventBus(20);
const fn1 = function() {
    console.log('print extra parameter', ...arguments);
    console.log('111111');
}
const fn2 = function() {
    console.log('222222');
}
eventBus.subscribe('test', fn1);
const { unSubscribe } = eventBus.subscribe('test', fn2);
unSubscribe();
eventBus.emit('test', 'extra parameter2');
// print extra parameter extra parameter2
// 111111
```