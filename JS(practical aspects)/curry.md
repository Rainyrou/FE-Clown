柯里化：将接收多个参数的函数转换为接收单一参数的函数，且返回接收剩余参数的新函数

```JavaScript
function _curry(fn) {
  function curried(...args) {
    const context = this;
    if (fn.length >= args.length) {
      return fn.apply(context, args);
    } else {
      return function (...newArgs) {
        return curried.apply(context, args.concat(newArgs));
      };
    }
  }
  return curried;
}
```

测试用例：

```js
function add(a, b, c) {
  return a + b + c;
}
const curriedAdd = _curry(add);
console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6
```

