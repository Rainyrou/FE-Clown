1. `var` 声明的变量有函数作用域，在同一作用域内可重复声明同一变量， JavaScript 引擎在预编译阶段将声明提升至其所在作用域的顶部，赋值无法提升，在声明前访问变量时，其值为 `undefined`
2. `let` 和 `const` 声明的变量有块级作用域，在同一作用域内无法重复声明同一变量（否则报错），`let/const` 声明的变量不被提升，在声明前访问变量时抛出 `ReferenceError`，因为变量处于暂时性死区。在 `let` 声明时访问变量，其值为 `undefined`，而 `const` 声明的变量必须初始化（否则报错），此外 `const` 声明的为只读常量引用，若其声明为基本数据类型，则该值无法修改，若其声明为引用数据类型，则该引用无法修改，但其内容可修改
3. 函数声明被提升至其所在作用域的顶部，可在声明前调用函数，函数声明可提升，函数体无法提升

```js
for (var i = 0; i < 3; i++) {
  var a = i;
  setTimeout(() => console.log(a), 1000); // 1 秒后批量执行 2 2 2
  setTimeout((i) => console.log(i), 1000); // 1 秒后批量执行 undefined undefined undefined
  setTimeout(() => console.log(i++), 1000); // 1 秒后批量执行 3 4 5
  console.log(i); // 0 1 2
}
```

```js
var a = 1;
function f() {
  console.log(a);
  var a = 2;
}
f(); // undefined
```

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
alert(0); // 0 2 3
```

函数无显隐返回语句则默认返回 `undefined`

```js
let a = 1;

function foo(a) {
  a = a + 1;
}

var b = foo(a);

function foo(a) {
  a = a + 2;
}

const c = foo(a);

function foo(a) {
  a = a + 3;
}

console.log(a, b, c); // 1 undefined undefined
```

全局变量：`var/let/const`  声明的全局变量 + 全局作用域中未声明已赋值的变量 + 全局对象 `window/global` 的属性变量
