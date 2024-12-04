JavaScript 基于原型，Prototype 和 Prototype Chain 为实现继承的一种机制，每个 JavaScript 对象在创建时关联原型对象，通过 ES5 `__proto__` 或 ES6 `Object.getPrototypeOf` 获得原型，原型链为 JavaScript 对象通过其原型与其他 JavaScript 对象相关联的链，当访问一个对象的属性或方法时，若在 JavaScript 对象本身没找到，则在其原型上查找，若原型上没找到，则继续在原型的原型上查找，直到找到该属性或方法或到达原型链末端，原型链末端为 `Object.prototype`，而 `Object.prototype` 的原型为 `null`，若在原型链的任何地方都找不到指定的属性或方法，则返回 `undefined`

普通函数的 `__proto__` 为 `Function.prototype`，因为 JavaScript 函数通过 `Function` 构造，而普通函数的 `prototype` 为一个对象，该对象的 `constructor` 属性指向函数本身

```js
function foo() {}
console.log(foo.__proto__ === Function.prototype); // true
console.log(foo.prototype.constructor === foo); // true
```

创建无原型的对象：

```js
const obj = Object.create(null);
```

`obj` 不继承 `Object.prototype` 的任何属性或方法，其 `[[Prototype]]` 内部属性设置为 `null` 即通过 `__proto__` 或 `Object.getPrototypeOf` 访问的原型

```js
Function.prototype.a = "Function";
Object.prototype.a = "Object";
function Person() {}
var child = new Person();
console.log(Person.a); // Function
console.log(child.a); // Object
console.log(child.__proto__.__proto__.constructor.constructor.constructor); // [Function: Function]
child.__proto__ // Person.prototype
child.__proto__.__proto__ // Object.prototype
child.__proto__.__proto__.constructor // Object
child.__proto__.__proto__.constructor.constructor // Function
```
