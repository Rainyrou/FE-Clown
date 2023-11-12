请补全 JavaScript 代码，完成"EventEmitter"类实现发布订阅模式

注意：

1. 同一名称事件可能有多个不同的执行函数
2. 通过"on"函数添加事件
3. 通过"emit"函数触发事件

![[1693373956796.png]]


```JavaScript
on(event, fn) {
    if (!this.events[event]) {
        this.events[event] = [fn];
    } else {
        this.events[event].push(fn);
    }
}
```

- `event`：事件名称
- `fn`：当事件被触发时要执行的回调函数
* 检查 `this.events` 是否已有给定的事件名。如果没有，它会为该事件名创建一个新数组，并将回调函数添加到这个新数组中。如果事件名已存在，它会将新的回调函数添加到该事件的现有回调数组中

```JavaScript
emit(event) {
    if (this.events[event]) {
        this.events[event].forEach(callback => callback());
    }
}
```

如果给定的事件在 `this.events` 对象中存在，代码会遍历该事件的所有回调函数，并执行它们