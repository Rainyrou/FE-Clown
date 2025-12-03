* 柯里化：将单个接收  n 个参数的函数转换为  n 个嵌套的接收单参数的函数
* `fn.length` 返回该函数期望接收的参数个数

```JavaScript
function _curry(fn) {
  function curried(...args) {
    const context = this;
    if (args.length >= fn.length) {
      return fn.apply(context, args);
    } else {
      return function (...newArgs) {
        return curried.apply(context, args.concat(newArgs));
      };
    }
  }
  return curried;
}

const curriedAdd = _curry((a, b, c) => a + b + c);
console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6
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
