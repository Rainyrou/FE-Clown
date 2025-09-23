参考文章：[「2021」高频前端面试题汇总](https://juejin.cn/post/6959043611161952269#heading-41)

- 对象方法调用时，`this` 指向该对象
- 普通函数/new 构造函数/IIFE调用时，`this` 指向全局对象

1. 语法：箭头函数提供更简洁的函数表达式语法，可隐式返回无需 `return` 语句，而普通函数必须显式返回
2. this：箭头函数不创建自己的 `this` 值，其捕获所在词法作用域（即父级上下文）的 `this` 值，因此箭头函数的 `this` 值是在定义时确定的；而普通函数的 `this` 值是在调用时动态确定的，单独调用时，`this` 指向全局对象（非严格模式）或 `undefined`（严格模式）
3. 构造函数：箭头函数无法作为构造函数，通过 `new` 关键字调用箭头函数时，JavaScript 运行时抛出 TypeError，这是因为箭头函数缺少作为构造函数的底层机制如 ECMAScript 规范要求的"作为构造函数的函数对象必须实现 `[[Construct]]` 内部方法"；而普通函数可作为构造函数，其 `this` 指向新创建的对象实例
4. `arguments` 对象：箭头函数不创建自己的 `arguments` 对象，其捕获所在词法作用域的 `arguments` 对象，而普通函数有其 `arguments` 对象，其为类数组，包含函数调用时传入的所有参数，通过索引访问各个参数且有表示参数数量的 `length` 属性
5. `prototype` 属性：箭头函数无，而普通函数有

```js
var fullname = "1";
var obj = {
  fullname: "2",
  prop: {
    fullname: "3",
    getFullname: () => {
      return this.fullname;
    },
  },
};
console.log(obj.prop.getFullname()); // 1
var test = obj.prop.getFullname;
console.log(test()); // 1
```

对象字面量不构成独立的词法作用域

String -> 字符串类型，类型转换函数调用
new String -> 对象类型，构造函数调用

```js
String('abc') instanceof String → false
new String('abc') instanceof String → true
```

类数组：有索引和 `length` 属性，不继承数组原型，无数组内置方法

* `arguments` 对象
* DOM 方法返回的 NodeList 如 `document.querySelectorAll('div')`
* `HTMLCollection`，某些 DOM 方法的返回值如 `document.getElementsByClassName`
