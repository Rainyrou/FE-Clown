在 JavaScript 中，原型(Prototype)和原型链(Prototype)是实现继承的一种机制。JavaScript 是基于原型的语言，这意味着对象可以从其他对象继承属性和方法

###### 原型（Prototype）

每个 JavaScript 对象在创建时都会与另一个对象关联，这个关联的对象就称为原型。每个对象都有一个内置的属性（在 ES5 中是 `__proto__`，在现代浏览器和 ES6 中可以通过 `Object.getPrototypeOf(obj)` 获得），指向它的原型

原型对象包含可被继承的共享属性和方法。这些被继承的属性和方法定义在构造器的 `prototype` 属性上

在 JavaScript 中，`.prototype` 是一个与函数相关的属性。每当你创建一个函数时，JavaScript 会自动为这个函数创建一个 `.prototype` 属性。这个属性是一个对象，它是实例可以继承的属性和方法的集合

1. 当你使用 `new` 关键字创建一个函数的新实例时，新对象内部的 `__proto__` 属性会被赋予创建它的函数的 `.prototype` 对象的引用。这意味着创建的对象可以访问其构造函数原型上的属性和方法
2. 这个原型链的机制使得任何在构造函数原型上定义的属性和方法，都可以在所有由这个构造函数创建的实例中被找到

例如，当你创建一个新的数组，你创建的对象会自动继承 `Array.prototype` 上的方法，如 `push`，`pop` 等

```JavaScript
let arr = [1, 2, 3];
console.log(arr.__proto__ === Array.prototype); // true
```

###### 原型链（Prototype Chain）

原型链是对象通过它们的原型与其他对象连接起来的链。当你尝试访问一个对象的属性或方法时，如果该对象自身没有这个属性或方法，JavaScript 会继续在该对象的原型上查找。如果原型上也没有，那么继续向上在原型的原型上查找，直到找到该属性或方法或者到达原型链的末端。原型链的末端通常是 `Object.prototype`。如果在原型链的任何地方都找不到指定的属性或方法，那么返回 `undefined`

这就是为什么所有的 JavaScript 对象都有比如 `toString` 方法，因为它们最终都会继承自 `Object.prototype`

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
