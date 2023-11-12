`Event.Bus` 是一种设计模式，常用于组件或模块间的通信。它允许多个组件、对象或模块订阅和广播事件，而无需显式地传递引用或依赖于其他组件。Event Bus 可以被视为一个中心点，其中一个组件（或模块、函数等）可以广播（发布）事件，而其他组件可以订阅这些事件并做出响应

实现核心思路点：

1. 创建一个  `eventMap`  集合用来存储事件，`key`  为事件名称，`value`  为事件数组列表
2. 实现一个 `subscribe` 订阅函数和一个 `emit` 发布函数

```JavaScript
class EventBus {
    constructor() {
        this.eventMap = {};
    }
   
    subscribe(eventName, callback) {
        if(!Reflect.has(this.eventMap, eventName)) {
            Reflect.set(this.eventMap, eventName, []);
        }
        this.eventMap[eventName].push(callback);
    }

    emit(eventName) {
        if(!Reflect.has(this.eventMap, eventName)) {
            console.warn(`never subscribe this event ${eventName}`);
            return;
        }
        const callbackList = this.eventMap[eventName];
        if(callbackList.length === 0) {
            console.warn(`event ${eventName} has no function to execute`);
            return;
        }
        for(const fn of callbackList) {
	        // 当调用 eventBus.emit('testName')时，`this` 指向 `eventBus` 的实例
	        fn.call(this);
         }
    }
};

const eventBus = new EventBus();

const fn1 = function() {
	console.log('111111');
}

const fn2 = function() {
	console.log('222222');
}

eventBus.subscribe('test', fn1);
eventBus.subscribe('test', fn2);
eventBus.subscribe('test2', fn1);
eventBus.emit('test');
```

增加功能：调用发布函数增加参数/最大订阅数量限制/取消订阅/清空事件/订阅一次（待补充）

原有的存储结构不能满足取消订阅功能，原有结构  `{ key: value }`，需要变更为  `{ key: { id1: value1, id2: value2, id3: value3 } }` , 这样取消订阅可根据每一个函数对应的  `id`  去取消(保证 id 的唯一性)

```JavaScript
class EventBus {
    constructor(maxListeners) {
        this.eventMap = {};
        this.maxListeners = maxListeners || Infinity;
        this.callbackId = 0;
    };

    subscribe(eventName, callback) {
        if(!Reflect.has(this.eventMap, eventName)) {
            Reflect.set(this.eventMap, eventName, {});
        }
        if(this.maxListeners !== Infinity && Object.keys(this.eventMap[eventName]).length >= this.maxListeners) {
            console.warn(`the event ${eventName} has exceeded the max listeners`);
            return;
        }
        const thisCallbackId = this.callbackId++;
        this.eventMap[eventName][thisCallbackId] = callback;
        const unSubscribe = () => {
            delete this.eventMap[eventName][thisCallbackId];
            if(Object.keys(this.eventMap[eventName]).length === 0) {
                delete this.eventMap[eventName];
            }
        };
	    return { unSubscribe };
    };

    emit(eventName, ...args) {
        if(!Reflect.has(this.eventMap, eventName)) {
            console.warn(`never subscribe the event ${eventName}`);
            return;
        }
        const callbackList = this.eventMap[eventName];
        if(Object.keys(callbackList).length === 0) {
            console.warn(`the event ${eventName} has no function to execute`);
            return;
        }
        for(const callback of Object.values(callbackList)) {
            callback.call(this, ...args);
        }
    };

    clear(eventName) {
        if(!eventName) {
            console.warn(`please provide the event name to be cleared`);
            return;
        }
        delete this.eventMap[eventName];
    };

    clearAll() {
        this.eventMap = {};
    };
};

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

正确的输出结果：

```
print extra parameter extra parameter2
111111
```

###### this.callbackId

- `EventBus` 类的一个属性，其初始值为 0。每次添加新的事件监听器时，这个 `callbackId` 会自增。它为每个新的事件监听器分配一个唯一的标识符（ID）。
- 由于 `this.callbackId` 是 `EventBus` 实例的属性，它能保证即使多个事件共享同一 `EventBus` 实例，每个事件监听器都有一个独一无二的 ID

###### thisCallbackId

- 在 `subscribe` 方法内创建的一个本地变量，它接收从 `this.callbackId` 自增得来的当前值。它用作添加到 `eventMap[eventName]` 中的回调函数的键（key）。每个回调函数都能通过它的 `thisCallbackId` 来被精确地引用和删除

1. 确保每个事件监听器都有一个唯一的引用，使得添加和移除监听器都能精确无误
2. 通过 ID 管理允许你维护一个复杂的事件监听器集合，即使它们对应的回调函数是相同的
3. 提供一个简便的方式来取消订阅特定的事件监听器，不会影响其他监听器

- 当调用 `eventBus.subscribe('test', fn1)` 时，`fn1` 函数被添加到 `eventMap` 中 `'test'` 事件的监听器列表中，并被分配了一个唯一的 ID
- 同理 `eventBus.subscribe('test', fn2)` 调用时，`fn2` 同样被添加，并被分配了一个唯一的 ID
- 当 `unSubscribe()` 被调用时，它使用闭包（closure）中保存的 `thisCallbackId` 来删除特定的事件监听器（在这个例子中是 `fn2`），而不影响其他监听器
- 最后当 `eventBus.emit('test', 'extra parameter2')`被调用时，它只会触发剩下的监听器（`fn1`），因为`fn2` 已经被取消订阅了


US,I can speak English and Chinese.I'm a Front-end engineer.I like coding.I'm preparing for an interview related to front-end.