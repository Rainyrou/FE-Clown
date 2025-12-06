`instanceof`  用于判断 JavaScript 对象的构造函数的  prototype 属性是否在该 JavaScript 对象的原型链上

```JavaScript
const _instanceof = (target, fn) => {
  let proto = Object.getPrototypeOf(target);
  while (proto) {
    if (proto === fn.prototype) return true;
    proto = Object.getPrototypeOf(proto);
  }
  return false;
};
```

```js
const _new = function (fn, ...args) {
  // 创建空对象
  // 并将其 [[Prototype]]（隐式原型）指向 fn.prototype（构造函数原型对象）
  const obj = Object.create(fn.prototype);
  const res = fn.apply(obj, args);
  return typeof res === "object" && res !== null ? res : obj;
};
```

