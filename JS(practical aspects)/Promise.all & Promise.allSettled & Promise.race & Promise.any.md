1. Promise.all

若通过 `push` 向结果数组添加值则导致结果数组的顺序 = 异步操作的完成顺序，而非输入的 `promises` 顺序

 ```js
Promise._all = (promises) => {
  return new Promise((resolve, reject) => {
    if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not an iterator`);
    promises = [...promises];
    if (promises.length === 0) return resolve([]);
    let count = 0;
    const values = [];
    promises.forEach((promise, index) =>
      Promise.resolve(promise)
        .then((res) => {
          values[index] = res;
          if (++count === promises.length) return resolve(values);
        })
        .catch(reject)
    );
  });
};

function test() {
  try {
    Promise._all(null).then(
      (res) => console.log(res),
      (rej) => console.log(rej)
    );
  } catch (e) {
    console.log(e); // TypeError: null is not a iterator
  }

  try {
    Promise._all({}).then(
      (res) => console.log(res),
      (rej) => console.log(rej)
    );
  } catch (e) {
    console.log(e); // TypeError: [object Object] is not a iterator
  }

  Promise._all([]).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // []
  
  Promise._all(new Set()).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // []

  Promise._all(new Map()).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // []

  Promise._all([
    Promise.resolve(1),
    Promise.resolve(2),
    Promise.resolve(3),
    4,
  ]).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // [ 1, 2, 3, 4 ]

  Promise._all([
    Promise.reject(1),
    Promise.resolve(2),
    Promise.resolve(3),
    4,
  ]).then(
    (res) => console.log(res),
    (rej) => console.log(rej)
  ); // 1
}

test();
```

2. Promise.allSettled

```js
Promise._allSettled = function (promises) {
  return new Promise((resolve, reject) => {
    if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not iterable`);
    promises = [...promises];
    if (promises.length === 0) return resolve([]);
    const values = [];
    let count = 0;
    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then(
          (value) => (values[index] = { status: "fulfilled", value }),
          (reason) => (values[index] = { status: "rejected", reason })
        )
        .finally(() => {
          if (++count === promises.length) return resolve(values);
        });
    });
  });
};
```

测试用例：

```js
Promise._allSettled([]).then((res) => console.log(res));
Promise._allSettled([Promise.resolve(1), Promise.resolve("a")]).then((res) =>
  console.log(res)
);
Promise._allSettled([
  Promise.resolve(1),
  Promise.reject(new Error("error")),
]).then((res) => console.log(res));
```

输出结果：

```js
[]
[
  { status: 'fulfilled', value: 1 },
  { status: 'fulfilled', value: 'a' }
]
[
  { status: 'fulfilled', value: 1 },
  {
    status: 'rejected',
    reason: Error: error
        at Object.<anonymous> (c:\Users\86158\Desktop\手写题\index.js:28:18)
        at Module._compile (node:internal/modules/cjs/loader:1105:14)
        at Object.Module._extensions..js (node:internal/modules/cjs/loader:1159:10)
        at Module.load (node:internal/modules/cjs/loader:981:32)
        at Function.Module._load (node:internal/modules/cjs/loader:822:12)
        at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:77:12)
        at node:internal/main/run_main_module:17:47
  }
]
```

3. Promise.race

```js
Promise._race = (promises) => {
  return new Promise((resolve, reject) => {
    if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not an iterator`);
    promises = [...promises];
    promises.forEach((promise) =>
      Promise.resolve(promise).then(resolve).catch(reject)
    );
  });
};

Promise._race([
  new Promise((resolve, reject) => setTimeout(() => reject("A"), 1000)),
  new Promise((resolve, reject) => setTimeout(() => resolve("B"), 2000)),
  new Promise((resolve, reject) => setTimeout(() => resolve("C"), 3000)),
])
  .then((res) => console.log(res))
  .catch((err) => console.log(err)); // A
```

4. Promise.any

```js
Promise._any = function (promises) {
  return new Promise((resolve, reject) => {
	if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not an iterator`);
	promises = [...promises];
    if (promises.length === 0) 
	    return reject(new AggregateError([], "No promises provided"));
    const errors = [];
    let count = 0,
      hasFulfilled = false;
    promises.forEach((promise, index) =>
      Promise.resolve(promise)
        .then((res) => {
          if (!hasFulfilled) {
            hasFulfilled = true;
            return resolve(res);
          }
        })
        .catch((err) => {
          errors[index] = err;
          if (++count === promises.length && !hasFulfilled)
            return reject(new AggregateError(errors, "All promises were rejected"));
        })
    );
  });
};

Promise._any([Promise.reject(1), Promise.resolve(2), Promise.reject(3)]).then(
  console.log
); // 2

Promise._any([Promise.reject("Error1"), Promise.reject("Error2")]).catch(
  (e) => {
    console.log(e.name); // AggregateError
    console.log(e.errors); // ['Error1', 'Error2']
    console.log(e.message); // All promises were rejected
  }
);
```