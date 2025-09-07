1. `var` 声明的变量具有函数作用域，在同一作用域内可重复声明同一变量， JavaScript 引擎在预编译阶段将声明提升至其所在作用域的顶部，赋值无法提升，在声明前访问变量时，其值为 `undefined`
2. `let` 和 `const` 声明的变量具有块级作用域，在同一作用域内无法重复声明同一变量（否则报错），`let` 和 `cons` 声明的变量不被提升，在声明前访问变量时抛出 `ReferenceError`，因为变量处于暂时性死区。在 `let` 声明时访问变量，其值为 `undefined`，而 `const` 声明的变量必须初始化（否则报错），此外 `const` 声明的为只读常量引用，若其声明为基本数据类型，则该值无法修改，若其声明为引用数据类型，则该引用无法修改，但其内容可修改
3. 函数声明被提升至其所在作用域的顶部部，可在声明前调用函数，函数声明可提升，函数体无法提升

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i++), 0); // 3 4 5
  console.log(i); // 0 1 2
}
```

```js
for (var i = 0; i < 5; i++) setTimeout(() => console.log(i), 5000); // 5 5 5 5 5
```

**`var`** 作用域为函数（全局）作用域，每次迭代回调均引用同一 `i` 变量，而非创建一个新的 `i` 变量

```js
var a = 1;
function f() {
  console.log(a);
  var a = 2;
}
f(); // undefined
```

在函数内部，`var` 声明的变量被提升到其所在作用域的顶部，但只提升声明，不提升赋值，因此在打印a时，局部变量已存在，其值为 `undefined` 且局部变量覆盖了全局变量

```js
function test() {
  var a = 1;
  setTimeout(function () {
    alert(a);
    a = 3;
  }, 1000);
  a = 2;
  setTimeout(function () {
    alert(a);
    a = 4;
  }, 3000);
}
test();
alert(0);
```

```
0 2 3
```

全局变量：

1. 隐式全局变量：非严格模式下，在全局作用域中为未声明的变量赋值
2. `var`、`let` 或 `const` 声明的全局变量：在全局作用域中通过 `var` 声明的变量为全局对象的属性（浏览器 -> `window` Node.js -> `global`
3. 在全局对象上定义的属性

