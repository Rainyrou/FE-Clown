JavaScript 是基于原型的语言，Prototype 和 Prototype Chain 是实现继承的一种机制

###### Prototype

每个 JavaScript 对象在创建时都会与另一个对象关联，这个关联的对象称为原型。每个对象都有一个内置属性（在 ES5 中是 `__proto__`，在ES6 中可以通过 `Object.getPrototypeOf(obj)` 获得），指向它的原型

每当你创建一个函数时，JavaScript 会自动为其创建一个 `.prototype` 属性，而 `.prototype` 是实例可以继承的属性和方法的集合。当你使用 `new` 关键字创建一个新实例时，新对象内部的 `__proto__` 属性会赋给创建它的函数的 `.prototype` 对象的引用


例如，当你创建一个新的数组，你创建的对象会自动继承 `Array.prototype` 上的方法，如 `push`，`pop` 等

```JavaScript
let arr = [1, 2, 3];
console.log(arr.__proto__ === Array.prototype); // true
```

###### Prototype Chain

原型链是对象通过其原型与其他对象相关联的链。当你访问一个对象的属性或方法时，若在对象本身没有找到，那么继续在该对象的原型上查找，如果原型上也没有，那么继续在原型的原型上查找，直到找到该属性或方法或到达原型链的末端，原型链的末端通常是 `Object.prototype`。如果在原型链的任何地方都找不到指定的属性或方法，那么返回 `undefined`

这就是为什么所有的 JavaScript 对象都有 `toString` 方法，因为它们最终都会继承自 `Object.prototype`

```JavaScript
let obj = {};
console.log(obj.__proto__ === Object.prototype); // true
console.log(obj.toString === Object.prototype.toString); // true
```

这里是一个如何使用原型和原型链的例子：

```JavaScript
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function () {
  console.log(`${this.name} makes a noise.`);
};

function Dog(name) {
  Animal.call(this, name); // 调用父类构造函数
}

// 设置Dog的原型为Animal的实例，建立原型链
Dog.prototype = Object.create(Animal.prototype);
// 设置Dog.prototype的构造器为Dog
Dog.prototype.constructor = Dog;

// 通过原型链添加方法
Dog.prototype.bark = function () {
  console.log(`${this.name} barks.`);
};

let d = new Dog('Mitzie');
d.speak(); // Mitzie makes a noise.
d.bark(); // Mitzie barks.
```

在上面的例子中，`Dog` 函数继承了 `Animal` 的 `speak` 方法，并通过原型链可以访问到这个方法。同时 `Dog` 的实例还拥有自己特有的 `bark` 方法
