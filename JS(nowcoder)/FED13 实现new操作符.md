请补全 JavaScript 代码，要求实现 new 操作符的功能

![[1693279920013.png]]

`_new` 函数的设计意图是模拟 JavaScript 中 `new` 操作符的行为。在 JavaScript 中，当我们使用 `new` 与某个构造函数一起使用时，我们实际上是要做以下事情：

1. 创建一个新的空对象
2. 将这个新对象的原型链（prototype）指向构造函数的 `prototype`
3. 使用构造函数初始化这个新对象
4. 如果构造函数返回了一个对象，则使用该对象，否则使用新创建的对象

为了实现这些操作，`_new` 函数需要知道两件事：

1. 哪个构造函数应该被调用 - 这就是 `fn` 参数的目的
2. 构造函数的参数是什么 - 这就是 `...args` 参数的目的

##### Function.prototype.apply()

[Function.prototype.apply() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

##### Object.create()

[Object.create() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

文档解析未完待续
