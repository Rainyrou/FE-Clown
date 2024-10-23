1. 语法：箭头函数提供一种更简洁的方式来书写函数表达式，普通函数相对冗长
2. this：箭头函数的 `this` 是在定义时确定的，其捕获所在词法作用域的 `this` 值。普通函数的 `this` 值是在调用时确定的，同一函数在不同上下文中多次调用时的 `this` 值不同，单独调用普通函数时，`this` 指向全局对象（非严格模式）或 `undefined`（严格模式）
3. 构造函数：箭头函数不能作为构造函数，即使通过 `new` 关键字调用箭头函数也不会创建一个新的 `this` 上下文，其`this` 值仍指向定义它时所在词法作用域的 `this` 值。`new` 关键字做了以下事情：创建一个新的空对象，将该新对象的原型指向构造函数的 `prototype`，使用构造函数初始化该新对象即将构造函数内的 `this` 绑定到新创建的对象上，若构造函数返回一个对象，则结果返回该对象，否则返回新创建的对象即 `this`。因为箭头函数没有 `this` 绑定，即无法执行"将构造函数内的 `this` 绑定到新创建的对象上"，同时箭头函数缺乏必要的底层机制来支持作为构造函数的操作如 ECMAScript 规范要求的"作为构造函数的函数对象必须实现 `[[Construct]]` 内部方法"，因此 JavaScript 运行时抛出"箭头函数不能用作构造函数"的类型错误。普通函数可作为构造函数，函数内的 `this` 指向新创建的对象实例
4. `arguments` 对象：箭头函数不绑定其 `arguments` 对象，其捕获所在词法作用域的 `arguments` 对象，而普通函数有其 `arguments` 对象，其为类数组，包含函数调用时传入的所有参数，可通过索引访问各个参数且有表示参数数量的 `length` 属性，可通过 `[...arguments]`、`Array.from` 或 `Array.prototype.slice.call(arguments)` 将其转化为数组
5. `prototype` 属性：箭头函数没有，普通函数有
6. 返回值：箭头函数可隐式返回无需 `return` 语句，而普通函数必须显式返回

###### 类数组

类数组具有索引和 `length` 属性，不继承数组原型，无数组内置方法

1. `arguments` 对象
2. DOM 方法返回的 NodeList 如 `document.querySelectorAll('div')`
3. `HTMLCollection`，某些 DOM 方法的返回值如 `document.getElementsByClassName`
