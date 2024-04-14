[「2021」高频前端面试题汇总之代码输出结果篇 - 掘金](https://juejin.cn/post/6959043611161952269#heading-32)

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

19. 

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
  setTimeout(() => console.log("timer1"), 0);
}
async function async2() {
  setTimeout(() => console.log("timer2"), 0);
  console.log("async2");
}
async1();
setTimeout(() => console.log("timer3"), 0);
console.log("start");
```

20. 

```js
async function async1() {
  console.log("async1 start");
  await new Promise((resolve) => console.log("promise1"));
  console.log("async1 success");
  return "async1 end";
}
console.log("srcipt start");
async1().then((res) => console.log(res));
console.log("srcipt end");
```

21. 

```js
async function async1() {
  console.log("async1 start");
  await new Promise((resolve) => {
    console.log("promise1");
    resolve("promise1 resolve");
  }).then((res) => console.log(res));
  console.log("async1 success");
  return "async1 end";
}
console.log("srcipt start");
async1().then((res) => console.log(res));
console.log("srcipt end");
```

22. 

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
console.log("script start");
setTimeout(() => console.log("setTimeout"), 0);
async1();
new Promise((resolve) => {
  console.log("promise1");
  resolve();
}).then(() => console.log("promise2"));
console.log("script end");
```

23. 

```js
async function async1() {
  await async2();
  console.log("async1");
  return "async1 success";
}
async function async2() {
  return new Promise((resolve, reject) => {
    console.log("async2");
    reject("error");
  });
}
async1().then((res) => console.log(res));
```

24. 

```js
async function async1() {
  await Promise.reject("error!!!").catch((e) => console.log(e));
  console.log("async1");
  return Promise.resolve("async1 success");
}
async1().then((res) => console.log(res));
console.log("script start");
```

25. 

```js
const first = () =>
  new Promise((resolve, reject) => {
    console.log(3);
    let p = new Promise((resolve, reject) => {
      console.log(7);
      setTimeout(() => {
        console.log(5);
        resolve(6);
        console.log(p);
      }, 0);
      resolve(1);
    });
    resolve(2);
    p.then((arg) => console.log(arg));
  });
first().then((arg) => console.log(arg));
console.log(4);
```

26. 

```js
const async1 = async () => {
  console.log("async1");
  setTimeout(() => console.log("timer1"), 2000);
  await new Promise((resolve) => console.log("promise1"));
  console.log("async1 end");
  return "async1 success";
};
console.log("script start");
async1().then((res) => console.log(res));
console.log("script end");
Promise.resolve(1)
  .then(2)
  .then(Promise.resolve(3))
  .catch(4)
  .then((res) => console.log(res));
setTimeout(() => console.log("timer2"), 1000);
```

27. 

```js
const p1 = new Promise((resolve) => {
  setTimeout(() => {
    resolve("resolve3");
    console.log("timer1");
  }, 0);
  resolve("resovle1");
  resolve("resolve2");
})
  .then((res) => {
    console.log(res);
    setTimeout(() => console.log(p1), 1000);
  })
  .finally((res) => console.log("finally", res));
```

28. 

```js
console.log("1");
setTimeout(function () {
  console.log("2");
  process.nextTick(() => console.log("3"));
  new Promise(function (resolve) {
    console.log("4");
    resolve();
  }).then(() => console.log("5"));
});
process.nextTick(() => console.log("6"));
new Promise(function (resolve) {
  console.log("7");
  resolve();
}).then(() => console.log("8"));
setTimeout(function () {
  console.log("9");
  process.nextTick(() => console.log("10"));
  new Promise(function (resolve) {
    console.log("11");
    resolve();
  }).then(() => console.log("12"));
});
```

29. 

```js
console.log(1);
setTimeout(() => console.log(2));
new Promise((resolve) => {
  console.log(3);
  resolve(4);
}).then((d) => console.log(d));
setTimeout(() => {
  console.log(5);
  new Promise((resolve) => resolve(6)).then((d) => console.log(d));
});
setTimeout(() => console.log(7));
console.log(8);
```

30. 

```js
console.log(1);
setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => console.log(3));
});
new Promise((resolve, reject) => {
  console.log(4);
  resolve(5);
}).then((data) => console.log(data));
setTimeout(() => console.log(6));
console.log(7);
```

31. 

```js
Promise.resolve()
  .then(() => {
    console.log("1");
    throw "Error";
  })
  .then(() => console.log("2"))
  .catch(() => {
    console.log("3");
    throw "Error";
  })
  .then(() => console.log("4"))
  .catch(() => console.log("5"))
  .then(() => console.log("6"));
```

32. 

```js
setTimeout(() => console.log(1), 100);
new Promise(function (resolve) {
  console.log(2);
  resolve();
  console.log(3);
}).then(function () {
  console.log(4);
  new Promise((resove, reject) => {
    console.log(5);
    setTimeout(() => console.log(6), 10);
  });
});
console.log(7);
console.log(8);
```