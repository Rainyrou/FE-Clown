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

