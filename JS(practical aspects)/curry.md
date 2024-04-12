柯里化：将接收多个参数的函数转换成接收单一参数的函数，且返回接收剩余参数的新函数

```JavaScript
function _mycurry(fn) {
  function curried(...args) {
    if (args.length >= fn.length) return fn.apply(this, args);
    else
      return function (...args2) {
        return curried.apply(this, args.concat(args2));
      };
  }
  return curried;
}
```

测试用例：

```js
function add(a, b, c) {
  return a + b + c;
}
const curriedAdd = _mycurry(add);
console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6
```

