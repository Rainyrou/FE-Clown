JavaScript 是基于原型的语言，Prototype 和 Prototype Chain 是实现继承的一种机制。每个 JavaScript 对象在创建时均关联原型对象，它们均有一个内置属性，通过 ES5 `__proto__` 或 ES6 `Object.getPrototypeOf` 获得。原型链是对象通过其原型与其他对象相关联的链，当你访问一个对象的属性或方法时，若在对象本身没有找到，则继续在该对象的原型上查找，若原型上也没有，则继续在原型的原型上查找，直到找到该属性或方法或到达原型链的末端，原型链的末端是 `Object.prototype`，若在原型链的任何地方找不到指定的属性或方法，则返回 `undefined`

