1. 在非严格模式下于全局作用域中直接为未声明的变量赋值
2. 在全局作用域中使用 `var` 声明变量，在浏览器环境中，全局对象是 `window`，在 Node.js 环境中，全局对象是 `global`
3. 在全局作用域中使用 `let` 或 `const` 声明变量
4. 在全局对象上定义属性

`var` 声明的作用域是其当前上下文的作用域，若其在函数内声明变量，它的作用域即函数内部，对全局命名空间无影响，但若 `var` 在全局作用域中声明变量，则有污染全局命名空间的风险

```js
function myFunction() {
    var localVariable = "我是局部变量";
}
console.log(localVariable); // ReferenceError: localVariable is not defined

var globalVariable = "我是全局变量";
console.log(globalVariable); // "我是全局变量"

if (true) {
    var blockVariable = "我是一个变量";
}
console.log(blockVariable); // "我是一个变量"
```

	

