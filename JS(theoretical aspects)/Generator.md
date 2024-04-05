Generator 即 ES6 关于协程的实现，其为一个封装的异步任务，以 `yield` 暂停执行函数，函数名前加星号。调用它不会立即执行函数体中的代码，而是返回指针对象。Generator 对象是一个迭代器对象，其 `next` 方法用于遍历函数内部的 `yield` 表达式，即分阶段执行 Generator 函数，每次调用 `next` 方法，均返回一个表示当前阶段信息的对象。`value` 是 `yield` 语句后表达式的值，表示当前阶段的值；`done` 为布尔值，表示函数是否执行完。`next` 方法返回的 `value` 是 Generator 函数的输出数据，`next` 方法接受的参数是外部向 Generator 函数的输入数据

```JavaScript
function* gen(x) {
	var y = yield x + 2;
	return y;
}

var g = gen(1);
g.next(); // { value: 3, done: false }
g.next(2); // { value: 2, done: true }
```

函数执行的返回值被当做 `yield` 的 `value`：

```JavaScript
function fn(num) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(num)
    }, 1000)
  })
}

function* gen() {
  yield fn(1);
  yield fn(2);
  return 3;
}

const g = gen();
console.log(g.next()); // { value: Promise { <pending> }, done: false }
console.log(g.next()); // { value: Promise { <pending> }, done: false }
console.log(g.next()); // { value: 3, done: true }
```

这不符合预期，我们想要的结果即两个 Promise 的结果，使用 `then` 方法即可：

```JavaScript
const g = gen();

const next1 = g.next();
next.value.then(res => {
	console.log(next1); // { value: Promise { 1 }, done: false }
	console.log(res1); // 1
})

const next2 = g.next()
next2.value.then(res2 => {
	console.log(next2); // { value: Promise { 2 }, done: false }
	console.log(res2); // 2
	console.log(g.next()); // { value: 3, done: true }
})
```
