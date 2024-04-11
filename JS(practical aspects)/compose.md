```js
function compose(arr) {
  return function (ctx) {
    function next() {
      if (arr.length) {
        let func = arr.shift();
        func(ctx, next);
      }
    }
    if (arr.length) {
      let func = arr.shift();
      func(ctx, next);
    }
  };
}

function func1(ctx, next) {
  ctx.index++;
  next();
}

function func2(ctx, next) {
  setTimeout(function () {
    ctx.index++;
    next();
  });
}

function func3(ctx, next) {
  console.log(ctx.index);
}

compose([func1, func2, func3])({ index: 0 }); // 2
```

`compose` 函数接收一个函数数组 `arr`，其中每个函数均接收两个参数即 `ctx` 上下文和 `next` 指向下一个中间件的函数，
`next` 函数递归调用自己从数组中取出并异步执行每个中间件，使用 `shift` 方法从数组中移除元素，使每个中间件只能执行一次
