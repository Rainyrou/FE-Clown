```JavaScript
Promise._all = function (promises) {
  return new Promise((resolve, reject) => {
	// 检查 `promises` 是否是 `null` 或 `undefined`
	// ES6 引入 Generator 对象，若一个对象可迭代，其有一个 `Symbol.iterator` 属性，对应值是一个函数
    if (promises == null || typeof promises[Symbol.iterator] !== "function")
      throw new TypeError(`${promises} is not an iterator`);
    // 将任何可迭代对象转化为数组
    promises = [...promises];
    if (promises.length === 0) resolve([]);
   
    // `count` 记录已 resolved 的 Promise 数量
    let count = 0;
    // `values` 存储各个 Promise 的结果值
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
function test() {
    try {
        Promise._all(null).then(res => console.log(res), rej => console.log(rej));
    } catch(e) {
        console.log(e); // TypeError: null is not a iterator
    }

    try {
        Promise._all({}).then(res => console.log(res), rej => console.log(rej));
    } catch(e) {
        console.log(e); // TypeError: [object Object] is not a iterator
    }

    Promise._all([]).then(res => console.log(res), rej => console.log(rej)); // []
    Promise._all(new Set()).then(res => console.log(res), rej => console.log(rej)); // []
    Promise._all(new Map()).then(res => console.log(res), rej => console.log(rej)); // []

    Promise._all([
        Promise.resolve(1),
        Promise.resolve(2),
        Promise.resolve(3),
        4
    ]).then(res => console.log(res), rej => console.log(rej)); // [ 1, 2, 3, 4 ]

    Promise._all([
        Promise.reject(1),
        Promise.resolve(2),
        Promise.resolve(3),
        4
    ]).then(res => console.log(res), rej => console.log(rej)); // 1
}

test();
```

