[PromisesA/mypromise.js at main · Rainyrou/PromisesA · GitHub](https://github.com/Rainyrou/PromisesA/blob/main/mypromise.js)

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
	        // `bind` 确保 `resolve` 和 `reject` 函数调用时使用的是 Promise 实例的上下文，从而正确修改 Promise 的状态
            func(this.resolve.bind(this), this.reject.bind(this));
        } catch(err) {
            this.reject(err);
        }
    };

    resolve(result) {
        if(this.PromiseState === myPromise.PENDING) {
            this.PromiseState = myPromise.FULFILLED;
            this.PromiseResult = result;
            this.onFulfilledCallbacks.forEach(callback => callback(result));
        }
    };

    reject(reason) {
        if(this.PromiseState === myPromise.PENDING) {
            this.PromiseState = myPromise.REJECTED;
            this.PromiseResult = reason;
            this.onRejectedCallbacks.forEach(callback => callback(reason));
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
    if (x === promise2)
	    throw new TypeError("Chaining cycle detected for promise");
    // 处理 Promise 实例
    if(x instanceof myPromise)
        x.then((y) => resolvePromise(promise2, y, resolve, reject), reject);
    // 处理函数和方法
    // 若 `x` 为一个函数或对象，则其可能为 "thenable" 对象，检查是否有 `.then` 方法，有则视其为一个 Promise
    else if(x !== null && ((typeof x === 'function') || (typeof x === 'object'))) {
        try {
            var then = x.then;
        } catch(err) {
            return reject(err);
        }
        if(typeof then === 'function') {
	        // 确保 `resolve` 或 `reject` 只被调用一次
	        // 处理不规范的 Promise 或 "thenable" 对象即多次调用 `resolve` 或 `reject`
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

```bash
npm install promises-aplus-tests -D
```

`package.json`

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

#### myPromise 类

##### constructor 部分

###### constructor 传入 func 作为参数

Promise 的核心是延迟执行和状态管理

1. 当创建一个 Promise 实例时，传入的函数 `func` 包含某个时刻要执行的异步操作。这个函数不会立即执行，而是在 Promise 内部被保存下来，直到 Promise 实例被 resolved 或 rejected
2. Promise 的状态管理需要在异步操作完成后进行，传入的函数 `func` 提供两个函数：`resolve` 和 `reject`，这两个函数分别用于在异步操作成功完成时 resolve Promise 或在发生错误时 reject Promise

```JavaScript
const myAsyncTask = new myPromise((resolve, reject) => {
    // ...
    setTimeout(() => {
        if(...) {
            resolve("result");
        } else {
            reject("reason");
        }
    }, 1000);
});

myAsyncTask.then(result => {
    console.log(result);
}).catch(reason => {
    console.log(reason);
});
```

##### resolve 部分

当某个 Promise 对象处于 pending 时，可以将其状态转换为 fulfilled，并执行与此 Promise 对象关联的所有成功回调

```JavaScript
if(this.PromiseState === myPromise.PENDING)
```

Promise/A+ 规范允许在 Promise 被 fulfilled 前后添加回调函数。因此，你可能会在创建 Promise 后的任何时间点调用 `then` 方法。这也是为什么我们需要 `onResolvedCallbacks` 数组的原因：它存储所有要在 Promise 被 fulfilled 时调用的成功回调

当你调用 Promise 的 `then` 方法时，你实际上注册一个回调函数，该函数在 Promise 被解决或拒绝时调用

##### then 部分

```JavaScript
this.onFulfilledCallbacks.push(() => { ... });
```

将函数放到 `onFulfilledCallbacks` 数组里（缓存回调函数），当 Promise fulfilled 时执行这些回调函数的逻辑

#### resolvePromise 部分

```JavaScript
resolvePromise(promise2, y, resolve, reject);
```

当 `x` 被解决并给出值 `y` 时，我们需要确定 `promise2` 的状态。为此，再次调用 `resolvePromise` 函数，这样我们可以递归地处理可能的 Promise 链，这是因为 `y` 可能是另一个 Promise 或"thenable"对象。因此我们需要递归处理，直到得到一个非 Promise 值或已知状态的 Promise

为了处理这些 Promise 链，我们需要确定一个 Promise 解决的值是否是另一个 Promise。如果是，那么我们必须等待那个 Promise fulfilled 或 rejected，以确定我们自己的 Promise 的状态
