1. `var` 声明的变量有函数作用域，在同一作用域内可重复声明同一变量， JavaScript 引擎在预编译阶段将声明提升至其所在作用域的顶部，赋值无法提升，在声明前访问变量时，其值为 `undefined`
2. `let` 和 `const` 声明的变量有块级作用域，在同一作用域内无法重复声明同一变量（否则报错），`let/const` 声明的变量不被提升，在声明前访问变量时抛出 `ReferenceError`，因为变量处于暂时性死区。在 `let` 声明时访问变量，其值为 `undefined`，而 `const` 声明的变量必须初始化（否则报错），此外 `const` 声明的为只读常量引用，若其声明为基本数据类型，则该值无法修改，若其声明为引用数据类型，则该引用无法修改，但其内容可修改
3. 函数声明被提升至其所在作用域的顶部，可在声明前调用函数，函数声明可提升，函数体无法提升

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i++), 0); // 3 4 5
  console.log(i); // 0 1 2
}
```

```js
for (var i = 0; i < 5; i++) setTimeout(() => console.log(i), 1000); // 5 5 5 5 5（立即输出）
```

`var` 声明的变量有函数作用域，变量 `i` 实际创建于 `for` 循环外部，所有 `setTimeout` 实例引用同一变量，其不阻塞主线程，立即执行下一` for` 循环，`setTimeout` 回调同时添加至任务队列，其延迟计时从 `setTimeout` 调用时开始，由于 `for` 循环微秒级的执行速度，5 个 `setTimeout` 的延迟计时几乎同步开始，1 秒后被批量取出执行，因此一次性输出五个 5 而非间隔 1 秒输出一个

```js
var a = 1;
function f() {
  console.log(a);
  var a = 2;
}
f(); // undefined
```

在函数内部，`var` 声明的变量被提升至其所在作用域的顶部，但只提升声明，不提升赋值，因此在打印 a 时，局部变量已存在，其值为 `undefined` 且局部变量已覆盖全局变量

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
// 0 2 3
```

函数无显隐  `return`  语句则执行完毕后默认返回  `undefined`

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
