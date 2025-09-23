`instanceof`  用于判断 JavaScript 对象的构造函数的  prototype 属性是否出现于该 JavaScript 对象的原型链上

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
  // 构造新的空对象，将其原型设置为构造函数的 `prototype`
  const obj = Object.create(fn.prototype);
  const res = fn.apply(obj, args);
  return typeof res === "object" && res !== null ? res : obj;
};
```

