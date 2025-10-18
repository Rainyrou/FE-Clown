参考文章：[「2021」高频前端面试题汇总](https://juejin.cn/post/6959043611161952269#heading-41)

- 对象方法调用时，`this` 指向该对象
- 普通函数/new 构造函数/IIFE调用时，`this` 指向全局对象
* new 绑定 > 显式绑定 > 隐式绑定 > 默认绑定

1. 语法：箭头函数可隐式返回，普通函数需显式返回
2. this：箭头函数不创建其 `this` 值，捕获所在词法作用域（即父级上下文）的 `this` 值，在定义时确定（无法通过 `apply/call/bind/new` 改变其 `this` 值），普通函数的 `this` 值是在调用时确定的，隐式绑定（对象方法）、显式绑定（`apply/call/bind`）、`new` 绑定或默认绑定（非严格模式 -> 全局 + 严格模式 -> undefined）
3. 构造函数：箭头函数无 `[[Construct]]` 内部方法，因此无法作为构造函数，无 `prototype` 属性，普通函数实现 `[[Construct]]` 内部方法，因此可作为构造函数，有 `prototype` 属性
4. `arguments` 对象：箭头函数无，普通函数有

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

```js
const obj = {
  value: 10,
  normal: function () {
    return this.value;
  },
  arrow: () => this.value,
};

console.log(obj.normal()); // 10
console.log(obj.arrow()); // undefined

const normal = obj.normal;
const arrow = obj.arrow;
const obj2 = { value: 20, normal, arrow };

console.log(obj2.normal()); // 20
console.log(obj2.arrow()); // undefined
```

对象字面量无法构成独立的词法作用域

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

第一次调用 -> 隐式绑定
第二次调用 -> 默认绑定，`bar` 函数内部单独调用 `printA`，其无对象引用、未使用 `new/call/apply`

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

变量名 `x`，在函数定义时确定 -> 沿作用域链查找
对象属性 `this.x`，在函数被调用时 -> `this` 指向的对象本身查找，忽略作用域链上命名相同的局部变量 `x`

```js
String('abc') instanceof String → false
new String('abc') instanceof String → true
```

String -> 字符串类型，类型转换函数调用
new String -> 对象类型，构造函数调用

类数组：有索引和 `length` 属性，不继承数组原型，无数组内置方法

* `arguments` 对象
* DOM 方法返回的 NodeList 如 `document.querySelectorAll('div')`
* `HTMLCollection`，某些 DOM 方法的返回值如 `document.getElementsByClassName`
