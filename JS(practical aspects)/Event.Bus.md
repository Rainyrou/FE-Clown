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
```

输出结果：

```
print extra parameter extra parameter2
111111
```