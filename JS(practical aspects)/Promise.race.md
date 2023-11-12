```JavaScript
Promise._race = function(promises) {
    if(promises == null || typeof promises[Symbol.iterator] !== 'function') {
        throw new TypeError(`${promises} is not a iterator`);
    }
    promises = [...promises];
    return new Promise((resolve, reject) => {
        for(let p of promises) {
            Promise.resolve(p).then(resolve).catch(reject);
        }
    });
};
```

由于只要有一个 Promise 完成，`resolve` 或 `reject` 就会被调用，这意味着整个 `Promise._race` 也会完成。而其他还未完成的 Promises 仍会继续执行直至完成，但它们的结果不会影响 `Promise._race` 的结果
