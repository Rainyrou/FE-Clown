**以 Chrome 上执行为准，而非 VSCode**

1.

```js
const promise = new Promise((resolve, reject) => {
  console.log(1);
  console.log(2);
});
promise.then(() => console.log(3));
console.log(4);
```

2.

```js
const promise1 = new Promise((resolve, reject) => {
  console.log("promise1");
  resolve("resolve1");
});
const promise2 = promise1.then((res) => console.log(res));
console.log("1", promise1);
console.log("2", promise2);
```

3.

```js
const promise = new Promise((resolve, reject) => {
  console.log(1);
  setTimeout(() => {
    console.log("timerStart");
    resolve("success");
    console.log("timerEnd");
  }, 0);
  console.log(2);
});
promise.then((res) => console.log(res));
console.log(4);
```

4.

```js
Promise.resolve().then(() => {
  console.log("promise1");
  const timer2 = setTimeout(() => console.log("timer2"), 0);
});
const timer1 = setTimeout(() => {
  console.log("timer1");
  Promise.resolve().then(() => console.log("promise2"));
}, 0);
console.log("start");
```

5.

```js
const promise = new Promise((resolve, reject) => {
  resolve("success1");
  reject("error");
  resolve("success2");
});
promise
  .then((res) => console.log("then:", res))
  .catch((err) => console.log("catch:", err));
```

6. 

```js
Promise.resolve(1).then(2).then(Promise.resolve(3)).then(console.log);
```

7. 

```js
const promise1 = new Promise((resolve, reject) =>
  setTimeout(() => resolve("success"), 1000)
);
const promise2 = promise1.then(() => {
  throw new Error("error!!!");
});
console.log("promise1", promise1);
console.log("promise2", promise2);
setTimeout(() => {
  console.log("promise1", promise1);
  console.log("promise2", promise2);
}, 2000);
```

8. 

```js
Promise.resolve(1)
  .then((res) => {
    console.log(res);
    return 2;
  })
  .catch((err) => 3)
  .then((res) => console.log(res));
```

9. 

```js
Promise.resolve()
  .then(() => new Error("error!!!"))
  .then((res) => console.log("then: ", res))
  .catch((err) => console.log("catch: ", err));
```

10. 

```js
const promise = Promise.resolve().then(() => promise);
promise.catch(console.err);
```

11. 

```js
Promise.reject("err!!!")
  .then(
    (res) => console.log("success", res),
    (err) => console.log("error", err)
  )
  .catch((err) => console.log("catch", err));
```

12. 

```js
Promise.resolve()
  .then(
    function success(res) {
      throw new Error("error!!!");
    },
    function fail1(err) {
      console.log("fail1", err);
    }
  )
  .catch(function fail2(err) {
    console.log("fail2", err);
  });
```

13. 

```js
Promise.resolve("1")
  .then((res) => console.log(res))
  .finally(() => console.log("finally"));
Promise.resolve("2")
  .finally(() => {
    console.log("finally2");
    return "我是 finally2 返回的值";
  })
  .then((res) => console.log("finally2 后面的 then 函数", res));
```

14. 

```js
Promise.resolve("1")
  .finally(() => {
    console.log("finally1");
    throw new Error("我是 finally 中抛出的异常");
  })
  .then((res) => console.log("finally 后面的 then 函数", res))
  .catch((err) => console.log("捕获错误", err));
```

15. 

```js
const runAsync = (x) =>
  new Promise((r) => setTimeout(() => r(x, console.log(x)), 1000));
Promise.all([runAsync(1), runAsync(2), runAsync(3)]).then((res) =>
  console.log(res)
);
```

16. 

```js
const runAsync = (x) =>
  new Promise((r) => setTimeout(() => r(x, console.log(x)), 1000));
const runReject = (x) =>
  new Promise((res, rej) =>
    setTimeout(() => rej(`Error: ${x}`, console.log(x)), 1000 * x)
  );
Promise.all([runAsync(1), runReject(4), runAsync(3), runReject(2)])
  .then((res) => console.log(res))
  .catch((err) => console.log(err));
```

16. 

```js
const runAsync = (x) =>
  new Promise((r) => setTimeout(() => r(x, console.log(x)), 1000));
Promise.race([runAsync(1), runAsync(2), runAsync(3)])
  .then((res) => console.log("result: ", res))
  .catch((err) => console.log(err));
```

17. 

```js
const runAsync = (x) =>
  new Promise((r) => setTimeout(() => r(x, console.log(x)), 1000));
const runReject = (x) =>
  new Promise((res, rej) =>
    setTimeout(() => rej(`Error: ${x}`, console.log(x)), 1000 * x)
  );
Promise.race([runReject(0), runAsync(1), runAsync(2), runAsync(3)])
  .then((res) => console.log("result: ", res))
  .catch((err) => console.log(err));
```

18. 

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
async1();
console.log("start");
```

