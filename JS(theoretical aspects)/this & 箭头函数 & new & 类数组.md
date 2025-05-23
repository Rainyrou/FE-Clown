参考文章：[「2021」高频前端面试题汇总](https://juejin.cn/post/6959043611161952269#heading-41)

```js
function foo() {
  console.log(this.a);
}
function doFoo() {
  foo();
}
var obj = {
  a: 1,
  doFoo: doFoo,
};
var a = 2;
obj.doFoo(); // 2
```

```js
var a = 10;
var obj = {
  a: 20,
  say: () => console.log(this.a),
};
obj.say(); // 10
var anotherObj = { a: 30 };
obj.say.apply(anotherObj); // 10
```

```js
function a() {
  console.log(this);
}
a.call(null); // window
```

```js
var obj = {
  name: "cuggz",
  fun: function () {
    console.log(this.name);
  },
};
obj.fun(); // cuggz
new obj.fun(); // undefined
```

```js
var obj = {
  say: function () {
    var f1 = () => console.log(this);
    f1();
  },
  pro: {
    getPro: () => console.log(this),
  },
};
var o = obj.say;
o(); // window
obj.say(); // obj
obj.pro.getPro(); // window
```

```js
var myObject = {
  foo: "bar",
  func: function () {
    var self = this;
    console.log(this.foo); // bar
    console.log(self.foo); // bar
    (function () {
      console.log(this.foo); // undefined
      console.log(self.foo); // bar
    })();
  },
};
myObject.func();
```

```js
window.number = 2;
var obj = {
  number: 3,
  db1: (function () {
    console.log(this);
    this.number *= 4;
    return function () {
      console.log(this);
      this.number *= 5;
    };
  })(),
};
var db1 = obj.db1;
db1();
obj.db1();
console.log(obj.number); // 15
console.log(window.number); // 40
```

```js
var a = 1;
function printA() {
  console.log(this.a);
}
var obj = {
  a: 2,
  foo: printA,
  bar: function () {
    printA();
  },
};
obj.foo(); // 2
obj.bar(); // 1
var foo = obj.foo;
foo(); // 1
```

```js
var x = 3;
var y = 4;
var obj = {
  x: 1,
  y: 6,
  getX: function () {
    var x = 5;
    return (function () {
      return this.x;
    })();
  },
  getY: function () {
    var y = 7;
    return this.y;
  },
};
console.log(obj.getX()); // 3
console.log(obj.getY()); // 6
```

```js
var a = 10;
var obt = {
  a: 20,
  fn: function () {
    var a = 30;
    console.log(this.a);
  },
};
obt.fn(); // 20
obt.fn.call(); // 10
```

```js
function a(xx) {
  this.x = xx;
  return this;
}
var x = a(5);
var y = a(6);
console.log(x.x); // undefined
console.log(y.x); // 6
```

```js
function foo(something) {
  this.a = something;
}
var obj1 = {
  foo: foo,
};
var obj2 = {};
obj1.foo(2);
console.log(obj1.a); // 2
obj1.foo.call(obj2, 3);
console.log(obj2.a); // 3
var bar = new obj1.foo(4);
console.log(obj1.a); // 2
console.log(bar.a); // 4
```

```js
function foo(something) {
  this.a = something;
}
var obj1 = {};
var bar = foo.bind(obj1);
bar(2);
console.log(obj1.a); // 2
var baz = new bar(3);
console.log(obj1.a); // 2
console.log(baz.a); // 3
```

```js
(function () {
  var x = (y = 1);
})();
var z;
console.log(y); // 1
console.log(z); // undefined
console.log(x); // Uncaught ReferenceError: x is not defined
```

```js
var a, b;
(function () {
  console.log(a); // undefined
  console.log(b); // undefined
  var a = (b = 3);
  console.log(a); // 3
  console.log(b); // 3
})();
console.log(a); // undefined
console.log(b); // 3
```

```js
var friendName = "World";
(function () {
  if (typeof friendName === "undefined") {
    var friendName = "Jack";
    console.log("Goodbye " + friendName);
  } else {
    console.log("Hello " + friendName);
  }
})(); // Goodbye Jack
```

```js
function fn1() {
  console.log("fn1");
}
var fn2;
fn1(); // fn1
fn2(); // Uncaught TypeError: fn2 is not a function
fn2 = function () {
  console.log("fn2");
};
fn2(); // fn2
```

```js
function a() {
  var temp = 10;
  function b() {
    console.log(temp);
  }
  b();
}
a(); // 10
function a() {
  var temp = 10;
  b();
}
function b() {
  console.log(temp);
}
a(); // Uncaught ReferenceError: temp is not defined
```

在 JavaScript 中，`this` 值由函数调用方式决定：

- 方法调用如 `obj.doFoo` 时，`this` 指向调用该方法的对象即 `obj`
- 普通函数调用如 `foo` 时，`this` 指向全局对象，在浏览器中为 `window`

`new` 构造函数和立即执行函数表达式的 `this` 均指向全局对象  

1. 语法：箭头函数提供一种更简洁的方式来书写函数表达式，普通函数相对冗长
2. this：箭头函数不创建自己的 `this` 值，其捕获所在词法作用域的 `this` 值即父级所处上下文，因此箭头函数的 `this` 值在定义时确定的，普通函数的 `this` 值是在调用时确定的，同一函数在不同上下文中多次调用时的 `this` 值不同，单独调用普通函数时，`this` 指向全局对象（非严格模式）或 `undefined`（严格模式）
3. 构造函数：箭头函数不能作为构造函数，即使通过 `new` 关键字调用箭头函数也不会创建一个新的 `this` 上下文，其 `this` 值仍指向定义它时所在词法作用域的 `this` 值。`new` 关键字做了以下事情：创建一个新的空对象，将该新对象的原型指向构造函数的 `prototype`，使用构造函数初始化该新对象即将构造函数内的 `this` 绑定到新创建的对象上，若构造函数返回一个对象，则结果返回该对象，否则返回新创建的对象即 `this`。因为箭头函数没有 `this` 绑定，即无法执行"将构造函数内的 `this` 绑定到新创建的对象上"，同时箭头函数缺乏必要的底层机制来支持作为构造函数的操作如 ECMAScript 规范要求的"作为构造函数的函数对象必须实现 `[[Construct]]` 内部方法"，因此 JavaScript 运行时抛出"箭头函数不能用作构造函数"的类型错误。普通函数可作为构造函数，函数内的 `this` 指向新创建的对象实例
4. `arguments` 对象：箭头函数不绑定其 `arguments` 对象，其捕获所在词法作用域的 `arguments` 对象，而普通函数有其 `arguments` 对象，其为类数组，包含函数调用时传入的所有参数，可通过索引访问各个参数且有表示参数数量的 `length` 属性，可通过 `[...arguments]`、`Array.from` 或 `Array.prototype.slice.call(arguments)` 将其转化为数组
5. `prototype` 属性：箭头函数没有，普通函数有
6. 返回值：箭头函数可隐式返回无需 `return` 语句，而普通函数必须显式返回

###### 类数组

类数组具有索引和 `length` 属性，不继承数组原型，无数组内置方法

1. `arguments` 对象
2. DOM 方法返回的 NodeList 如 `document.querySelectorAll('div')`
3. `HTMLCollection`，某些 DOM 方法的返回值如 `document.getElementsByClassName`
