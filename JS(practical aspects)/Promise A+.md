```JavaScript
class myPromise {
    static PENDING = 'pending';
    static FULFILLED = 'fulfilled';
    static REJECTED = 'rejected';

    constructor(func) {
        this.PromiseState = myPromise.PENDING;
        this.PromiseResult = null;
        // 存储 `then` 方法注册的回调函数
        this.onFulfilledCallbacks = [];
        this.onRejectedCallbacks = [];
        try {
            func(this.resolve.bind(this), this.reject.bind(this));
        } catch(err) {
            this.reject(err);
        }
    };

    resolve(result) {
        if(this.PromiseState === myPromise.PENDING) {
            this.PromiseState = myPromise.FULFILLED;
            this.PromiseResult = result;
            this.onFulfilledCallbacks.forEach(callback => {
                callback(result);
            })
        }
    };

    reject(reason) {
        if(this.PromiseState === myPromise.PENDING) {
            this.PromiseState = myPromise.REJECTED;
            this.PromiseResult = reason;
            this.onRejectedCallbacks.forEach(callback => {
                callback(reason);
            })
        }
    };

    then(onFulfilled, onRejected) {
        let promise2 = new myPromise((resolve, reject) => {
            if(this.PromiseState === myPromise.FULFILLED) {
                setTimeout(() => {
                    try {
                        if(typeof onFulfilled !== 'function') {
                            resolve(this.PromiseResult);
                        } else {
                            let x = onFulfilled(this.PromiseResult);
                            resolvePromise(promise2, x, resolve, reject);
                        }
                    } catch(err) {
                        reject(err);
                    }
                })
            } else if(this.PromiseState === myPromise.REJECTED) {
                setTimeout(() => {
                    try {
                        if(typeof onRejected !== 'function') {
                            reject(this.PromiseResult);
                        } else {
                            let x = onRejected(this.PromiseResult);
                            resolvePromise(promise2, x, resolve, reject);
                        }
                    } catch(err) {
                        reject(err);
                    }
                })
            } else if(this.PromiseState === myPromise.PENDING) {
                this.onFulfilledCallbacks.push(() => {
                    setTimeout(() => {
                        try {
                            if(typeof onFulfilled !== 'function') {
                                resolve(this.PromiseResult);
                            } else {
                                let x = onFulfilled(this.PromiseResult);
                                resolvePromise(promise2, x, resolve, reject);
                            }
                        } catch(err) {
                            reject(err);
                        }
                    })
                })
                this.onRejectedCallbacks.push(() => {
                    setTimeout(() => {
                        try {
                            if(typeof onRejected !== 'function') {
                                reject(this.PromiseResult);
                            } else {
                                let x = onRejected(this.PromiseResult);
                                resolvePromise(promise2, x, resolve, reject);
                            }
                        } catch(err) {
                            reject(err);
                        }
                    })
                })
            }
        })
        return promise2;
    };
};

function resolvePromise(promise2, x, resolve, reject) {
	// 处理循环引用
    if(x === promise2) {
        throw new TypeError('Chaining cycle detected for promise');
    }
    // 处理 Promise 实例
    if(x instanceof myPromise) {
        x.then(y => {
            resolvePromise(promise2, y, resolve, reject);
        }, reject)
    // 处理函数和方法
    // 如果 `x` 是一个对象或函数，那么它可能是一个 "thenable" 对象
    // 检查它是否有一个 `.then` 方法，如果有将之视为一个 Promise
    } else if(x !== null && ((typeof x === 'function') || (typeof x === 'object'))) {
        try {
            var then = x.then;
        } catch(err) {
            return reject(err);
        }
        if(typeof then === 'function') {
	        // 确保 `resolve` 或 `reject` 只被调用一次
	        // 处理不规范的 Promise 或 "thenable" 对象（尝试多次调用 `resolve` 或 `reject`）
            let called = false;
            try {
	            // `this` 指向 `x`
                then.call(
                    x,
                    y => {
                        if(called) return;
                        called = true;
                        resolvePromise(promise2, y, resolve, reject);
                    },
                    r => {
                        if(called) return;
                        called = true;
                        reject(r);
                    }
                )
            } catch(err) {
                if(called) return;
                called = true;
                reject(err);
            }
        } else {
            resolve(x);
        }
    } else {
        return resolve(x);
    }
};

myPromise.deferred = function() {
    let result = {};
    result.promise = new myPromise((resolve, reject) => {
        result.resolve = resolve;
        result.reject = reject;
    })
    return result;
}

module.exports = myPromise;
```

##### 官方测试用例

在命令行下执行：

```bash
npm install promises-aplus-tests -D
```

配置 `package.json`

```json
{
    "devDependencies": {
        "promises-aplus-tests": "^2.1.2"
    },
    "scripts": {
        "test": "promises-aplus-tests myPromise"
    }
}
```

##### 基础知识

pending 状态下的 Promise 不会执行回调函数 `then()`

必须给 Promise 对象传入一个执行函数，否则将会报错

尽量避免在 `then()` 方法里面定义 rejected 状态的回调函数（即 `then` 的第二个参数），而总是使用 `catch` 方法

Event Loop：它在 JavaScript 运行时环境检查执行栈是否为空。如果执行栈为空，它会从消息队列中取出一个消息并执行它。如果执行栈不为空，它会等待直到栈为空

执行栈是一个后进先出（LIFO）的数据结构，用于跟踪函数执行的过程。当一个函数被调用，它被添加到栈的顶部。当这个函数执行完毕，它会从栈顶弹出，返回控制权给调用它的函数

Macrotasks：setTimeout、setInterval、setImmediate、requestAnimationFrame、I/O、UI rendering 等

Microtasks：Promise 的 then 方法、process.nextTick、MutationObserver

1. 执行整体的 script 作为一个宏任务

2. 执行当前宏任务中的所有代码

3. 如果在执行当前宏任务时产生了微任务，在当前宏任务结束后立即执行这些微任务，直到微任务队列清空

4. 如果有任何待处理的 UI 渲染任务，此时处理

5. 执行下一个宏任务

6. 重复步骤 2-5

#### myPromise 类

##### constructor 部分

`this.resolve.bind(this)` 和 `this.reject.bind(this)` 中的 `.bind(this)` 部分确保 `resolve` 和 `reject` 函数在被调用时使用的是 Promise 实例的上下文（即 `this`）。在 JavaScript 中，函数的上下文通常是由调用它的对象决定的。通过使用 `.bind(this)`，我们确保这两个函数不管在哪里被调用，它们都将引用原始 Promise 对象，这样它们可以正确地更改 Promise 的状态和值

`call`、`apply` 和 `bind` 都可以改变函数体内部 this 的指向，但 `bind` 和 `call/apply` 有一个很重要的区别：一个函数被 `call/apply` 时，会立即执行函数；但 `bind` 只创建一个新函数，不会立即执行

因此 `this.resolve.bind(this)` 和 `this.reject.bind(this)` 只是作为参数，并没有直接执行

##### resolve 部分

当某个 Promise 对象处于 pending 时，可以将其状态转换为 fulfilled，并执行与此 Promise 对象关联的所有成功回调

```JavaScript
if(this.PromiseState === myPromise.PENDING)
```

Promise/A+ 规范明确指出，一旦 Promise 的状态从 pending 更改为 fulfilled 或 rejected，它就不能再次更改

Promise/A+ 规范允许在 Promise 被 fulfilled 之前或之后添加回调函数。因此，你可能会在创建 Promise 后的任何时间点调用 `then` 方法。这也是为什么我们需要 `onResolvedCallbacks` 数组的原因：它存储了所有要在 Promise 被 fulfilled 时调用的成功回调

当你调用 Promise 的 `then` 方法时，你实际上是在注册一个回调函数，该函数将在 Promise 被解决或拒绝时调用

##### then 部分

```JavaScript
this.onFulfilledCallbacks.push(() => { ... });
```

将函数放到 `onFulfilledCallbacks` 数组里（缓存回调函数），当 Promise fulfilled 时执行这些回调函数的逻辑

#### resolvePromise 部分

```JavaScript
x instanceof myPromise
```

`instanceof` 用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。这里我们检查 x 是否是通过`myPromise` 构造函数创建的。如果是，那么 x 就是一个 `myPromise` 实例

```JavaScript
resolvePromise(promise2, y, resolve, reject);
```

当 `x` 被解决并给出值 `y` 时，我们需要确定 `promise2` 的状态。为此，再次调用 `resolvePromise` 函数，这样我们可以递归地处理可能的 Promise 链，这是因为 `y` 可能是另一个 Promise（或"thenable"对象）。因此我们需要递归处理，直到得到一个非 Promise 值或已知状态的 Promise

为了处理这些 Promise 链，我们需要确定一个 Promise 解决的值是否是另一个 Promise。如果是，那么我们必须等待那个 Promise fulfilled 或 rejected，以确定我们自己的 Promise 的状态
