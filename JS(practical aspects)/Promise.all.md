#### Promise.all

`Promise.all`  静态方法接受一个 Promise 可迭代对象作为输入，并返回一个 Promise。当所有输入的 Promise 都被兑现时，返回的 Promise 也将被兑现（即使传入的是一个空的可迭代对象），并返回一个包含所有兑现值的数组。如果输入的任何 Promise 被拒绝，则返回的 Promise 将被拒绝，并带有第一个被拒绝的原因

```JavaScript
Promise.all(iterable)
```

参数：`iterable` 一个可迭代的对象，如 Array 或 String

返回值：`Promise`，其状态为：

- already fulfilled：如果传入的  `iterable`  为空
- asynchronously fulfilled：如果给定的  `iterable`  中所有的 promise 都已兑现。兑现值是一个数组，其元素顺序与传入的 promise 一致，而非按照兑现的时间顺序排列。如果传入的  `iterable`  是一个非空但不包含 pending promise，则返回的 promise 依然是异步兑现，而非同步兑现
- asynchronously rejected：如果给定的  `iterable`  中的任意 promise 被拒绝。拒绝原因是第一个拒绝的 promise 的拒绝原因

```JavaScript
Promise._all = function (promises) {
  return new Promise((resolve, reject) => {
    if (promises == null || typeof promises[Symbol.iterator] !== "function") {
      throw new TypeError(`${promises} is not an iterator`);
    }
    promises = [...promises];
    if (promises.length === 0) resolve([]);
    let count = 0;
    const values = [];
    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then((res) => {
          values[index] = res;
          if (++count === promises.length) resolve(values);
        })
        .catch(reject);
    });
  });
};
```

测试用例：

```JavaScript
// 1. 基本测试
const p1 = Promise.resolve(1);
const p2 = new Promise((resolve) => setTimeout(() => resolve(2), 100));
const p3 = new Promise((resolve) => setTimeout(() => resolve(3), 50));

Promise._all([p1, p2, p3]).then((values) => {
    console.log(values); // 应该输出 [1, 2, 3]
});

// 2. 拒绝的 Promise
const p4 = new Promise((_, reject) => setTimeout(() => reject('Error'), 100));

Promise._all([p1, p4, p3]).catch((error) => {
    console.log(error); // 应该输出 'Error'
});

// 3. 非 Promise 值的测试
Promise._all([p1, 4, p3]).then((values) => {
    console.log(values); // 应该输出 [1, 4, 3]
});

// 4. 空数组
Promise._all([]).then((values) => {
    console.log(values); // 应该输出 []
});
```

输出结果：

```
[]
[ 1, 4, 3 ]
[ 1, 2, 3 ]
Error
```

`count` 记录已经成功解析的 promise 数量

`values` 数组存储每个 promise 的结果值

当所有的 promises 都解析完毕时，返回的 promise 会被解析，并且结果值是 `values` 数组

##### 底层原理

`Promise._all` 是定义在 `Promise` 构造函数上的一个静态方法，它不是原型方法

###### 双等号

`if(promises == null)` 检查 `promises` 是否是 `null` 或 `undefined`。这是因为在 JavaScript 中，`null` 和 `undefined` 会在双等号比较时被视为相等

###### 拓展语法

```JavaScript
promises = [...promises];
```

这里使用扩展语法来将任何可迭代的对象转化为数组，确保后续的操作在一个固定的数组结构上进行，而不是其他的可迭代结构

因为后续代码中的 `promises.length` 和 `promises.forEach` 要求 `promises` 是一个数组或者类数组的对象。而且，这样做还可以确保我们不会在后续的代码中不小心修改原始的可迭代对象

###### 关于 `typeof promises[Symbol.iterator] !== 'function'`

JavaScript 的迭代指的是遍历集合中的每个元素。为了支持这种迭代操作，ES6 引入了迭代协议和迭代器协议

如果一个对象可迭代，它必须实现迭代协议。这意味着对象应该有一个名为 `Symbol.iterator` 的属性

`Symbol.iterator` 属性对应的值是一个函数，该函数返回一个遵循迭代器协议的对象。大多数内置的可迭代类型，如 Array、String、Map 和 Set，都已实现了该符号，因此它们都是可迭代的。迭代器对象必须具有一个 `next()` 方法，当调用时，返回一个包含两个属性（`value` 和 `done`）的对象

在 JavaScript 中，`Symbol.iterator` 是一个特殊的内建 Symbol。Symbols 是 JavaScript 中的新原始数据类型，每个 Symbol 都是唯一的

当你这样写 `promises[Symbol.iterator]`，你实际上是在访问该对象的默认迭代器方法

如果该方法不存在或者不是一个函数，那么 `promises` 就不是一个真正的可迭代对象

###### 关于 `Promise.resolve()`

对于 `promises` 数组中的每个 `promise`，使用 `Promise.resolve()` 确保它是一个 promise 对象。这意味着你可以在 `promises` 数组中传入非 promise 对象，它们会被视为已解析的 promise

`Promise.resolve()` 是一个静态方法，它返回一个新的 Promise 对象，该对象解析为给定值。如果该值已是一个 Promise 对象，则直接返回这个 Promise。否则返回一个解析为给定值的新 Promise。这种方法将非 Promise 值和 Promise 值统一处理

###### 关于 `resolve(values)`

这里的 `resolve` 是新创建的 `Promise` 的 `resolve` 方法

`if(++count === promises.length) resolve(values);` 这段代码保证只有当所有的 promises 都已经解析完成时，我们才解析 `Promise._all` 返回的新 Promise。而 `values` 数组包含了所有 promises 的解析值，它们的顺序与传入的 promises 相同

###### 核心区别

- `Promise.resolve()` 是一个静态方法，用于确保得到一个 Promise。它可以将任何值转化为 Promise

- 在 `new Promise()` 的上下文中，`resolve()` 函数是一个允许你解析 Promise 的工具。它不会创建新的 Promise，只是改变现有 Promise 的状态和值

###### 输出顺序

`Promise` 是异步的，但它们的状态改变是同步的。这意味着，当一个 Promise 被解析或拒绝，相应的 `.then` 或 `.catch` 回调会被放入 JavaScript 的事件循环的微任务队列中，并在当前执行堆栈完成后尽快执行

当你在 JavaScript 中看到连续的 Promise 操作，它们的执行顺序并不是立即的，而是基于它们的解析时机

###### 测试用例（优化）

```JavaScript
function test() {
    try {
        Promise._all(null).then(res => console.log(res), rej => console.log(rej));
        // TypeError: null is not a iterator
    } catch(e) {
        console.log(e);
    }

    try {
        Promise._all({}).then(res => console.log(res), rej => console.log(rej));
        // TypeError: [object Object] is not a iterator
    } catch(e) {
        console.log(e);
    }

    Promise._all([]).then(res => console.log(res), rej => console.log(rej));
    // []

    Promise._all(new Set()).then(res => console.log(res), rej => console.log(rej));
    // []

    Promise._all(new Map()).then(res => console.log(res), rej => console.log(rej));
    // []

    Promise._all([
        Promise.resolve(1),
        Promise.resolve(2),
        Promise.resolve(3),
        4
    ]).then(res => console.log(res), rej => console.log(rej));
    // [ 1, 2, 3, 4 ]

    Promise._all([
        Promise.reject(1),
        Promise.resolve(2),
        Promise.resolve(3),
        4
    ]).then(res => console.log(res), rej => console.log(rej));
    // 1
}

test();
```
