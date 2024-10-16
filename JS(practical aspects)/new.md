```js
const _new = function (fn, ...args) {
  // 构造一个新的空对象
  // 将其原型设置为构造函数的 `prototype`
  const obj = Object.create(fn.prototype);
  // `apply` 确保 `this` 在构造函数内部指向新创建的对象 `obj`
  const res = fn.apply(obj, args);
  return typeof res === "object" && res !== null ? res : obj;
};
```

为实现上述操作，`_new` 函数需知道两件事：

1. 哪个构造函数应被调用 -> `fn`
2. 构造函数的参数是什么 -> `...args`
