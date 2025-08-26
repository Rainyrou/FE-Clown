`instanceof`  用于检测 JavaScript 对象的构造函数之  prototype  属性是否出现在该 JavaScript 对象的原型链上

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
