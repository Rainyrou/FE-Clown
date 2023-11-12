请补全 JavaScript 代码，要求通过寄生组合式继承使"Chinese"构造函数继承于"Human"构造函数。要求如下：

1. 给"Human"构造函数的原型上添加"getName"函数，该函数返回调用该函数对象的"name"属性
2. 给"Chinese"构造函数的原型上添加"getAge"函数，该函数返回调用该函数对象的"age"属性

![[1693361120431.png]]

在 JavaScript 中，`this` 关键字引用的对象通常取决于函数如何被调用。它不是在定义时确定的，而是在运行时确定的。在构造函数中，`this` 通常指的是新创建的对象实例

```JavaScript
Human.call(this, name);
```

- 调用 `Human` 函数。使用 `this` 将 `Human` 函数的上下文设置为当前 `Chinese` 的实例。当 `Human` 函数内部引用 `this` 时，实际上引用的是 `Chinese` 的新实例
- 目的是使 `Chinese` 实例继承 `Human` 的所有实例属性。通过这种方式，我们可以确保所有由 `Human` 构造函数初始化的属性（如 `name`, `kingdom`, `color` 等）都会被初始化在新创建的 `Chinese` 实例上

* 你只需要传递 `name` 参数，因为 `kingdom` 和 `color` 在 `Human` 构造函数内部是固定的，并不需要外部参数来设定

```JavaScript
Chinese.prototype = Object.create(Human.prototype);
Chinese.prototype.constructor = Chinese;
```

- 使用 `Object.create` 方法来创建一个新对象，它的原型是(即它指向) `Human.prototype`。这确保了 `Chinese` 实例可以访问 `Human` 原型上的方法和属性
- 由于重写了 `Chinese.prototype`，需要修复其构造函数引用，确保它指向 `Chinese`。原来的 `Chinese.prototype` 对象有一个`constructor` 属性，指向 `Chinese` 函数本身。但当你重新为 `Chinese.prototype` 赋值时，新对象的 `constructor` 属性默认指向`Object`，而不是 `Chinese`
- 这种设置原型链的方式允许子类继承父类的所有原型方法和属性，同时保留正确的构造函数引用。如果不这样做，`Chinese` 的实例将无法访问 `Human` 的原型方法，且其 `constructor` 属性会错误地指向 `Object`，这可能导致未预期的行为和难以调试的错误
