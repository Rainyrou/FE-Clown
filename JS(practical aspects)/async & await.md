#### 异步编程

异步编程的语法目标，就是怎样让它更像同步编程

所谓异步，简单说就是将一个任务分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段

在 ES6 以前，异步编程的方法大概有：

1. Callbacks：当一个异步操作完成时，一个预先定义的回调函数将被调用

回调函数可能导致 Callback Hell，这是因为多重异步操作需要嵌套多层回调函数，从而导致代码难以阅读和维护

2. Event Listeners：一些对象（如 DOM 元素或 Node.js 中的某些对象）允许你添加事件监听器来监听并响应异步事件

3. Timers：使用 `setTimeout` 和 `setInterval` 延迟或定期执行代码

4. Pub/Sub or Observer pattern：这是一种对象间的通信模式，其中一个对象（发布者）将事件的发生通知给多个其他对象（订阅者）

5. Streams in Node.js：在 Node.js 中，流是处理数据的异步方式，特别是当数据量大到无法一次性加载到内存中时

#### Generator

Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。它不同于普通函数，是可以暂停执行的，所以函数名之前要加星号，以示区别

整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用 `yield` 语句注明

调用 Generator 函数不会返回结果，返回的是指针对象，即当调用 Generator 函数时，它不会立即执行函数体中的代码，而是返回一个 Generator 对象。Generator 对象是一个特殊的迭代器对象，遵循迭代器协议。它实现了一个 `next` 方法，用于遍历 Generator 函数内部的 `yield` 表达式，即分阶段执行 Generator 函数。每次调用 `next` 方法，会返回一个对象，表示当前阶段的信息（ `value` 属性和 `done` 属性）。`value` 属性是 `yield` 语句后面表达式的值，表示当前阶段的值；`done` 属性是一个布尔值，表示 Generator 函数是否执行完毕，即是否还有下一个阶段

`next` 方法返回值的 `value` 属性，是 Generator 函数向外输出数据；`next` 方法可以接受参数，这是向 Generator 函数体内输入数据

```JavaScript
function* gen(x) {
	var y = yield x + 2;
	return y;
}

var g = gen(1);
g.next(); // { value: 3, done: false }
g.next(2); // { value: 2, done: true }
```

函数执行返回值会当做 `yield` 对象的 `value` 值：

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

这不符合预期，其实我们想要的结果是两个 Promise 的结果，使用 Promise 的 `then` 方法即可：

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

#### async/await 剖析

`JavaScript` 是单线程的，为避免同步阻塞可能会带来的一些负面影响，引入了异步非阻塞机制，而对于异步执行的解决方案从最早的回调函数，到 `ES6` 的 `Promise` 对象以及 `Generator` 函数，每次都有所改进，但却又美中不足，它们都有额外的复杂性，需要深刻理解抽象的底层运行机制，直到在 `ES7` 中引入了 `async/await`，它可以简化使用多个 `Promise` 时的同步行为，在编程时甚至不需要关心这个操作是否为异步操作

`async/await` 实际上是 `Generator` 函数的语法糖，如 `Promise` 类似于结构化回调，`async/await` 在实现上结合了 `Generator` 函数与 `Promise` 函数

#### 手写 async/await

`async/await` 在实现上结合了 `Generator` 函数与 `Promise` 函数，区别在于：

- gen 函数执行返回值不是 Promise，asyncFn 执行返回值是 Promise
- gen 函数需要执行相应的操作，才能等同于 asyncFn 的排队效果
- gen 函数执行的操作是不完善的，因为并不确定有几个 yield，不确定会嵌套几次

据此有：

```JavaScript
// `generatorToAsync` 是一个高阶函数，它接收一个 Generator 函数
function generatorToAsync(generatorFn) {
  return function () {
	 // 执行 `generatorFn` 并返回一个迭代器 `gen`	 
    const gen = generatorFn.apply(this, arguments);
    // 返回一个 Promise
    return new Promise((resolve, reject) => {
		    // `go` 用于递归 `gen`
	        // `key` 可以是 'next' 或 'throw'，而 `arg` 是传递给生成器的值
      function go(key, arg) {
        let res;
        try {
          res = gen[key](arg);
        } catch (err) {
          return reject(err);
        }
        const { value, done } = res;
         // 使用 `Promise.resolve(value)` 确保返回的值是一个 Promise
	     // 允许在 `yield` 表达式后面使用非 Promise 值
        return done
          ? resolve(value)
          : Promise.resolve(value).then(
              (val) => go("next", val),
              (err) => go("throw", err)
            );
      }
      // 首次执行
      go("next");
    });
  };
}

function fn(num) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(num * 2);
    }, 1000);
  });
}

function* gen() {
  const num1 = yield fn(1);
  console.log(num1);
  const num2 = yield fn(num1);
  console.log(num2);
  const num3 = yield fn(num2);
  console.log(num3);
  return num3;
}

generatorToAsync(gen)().then((res) => console.log(res));
```
