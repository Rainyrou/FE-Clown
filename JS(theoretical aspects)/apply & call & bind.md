`apply`、`call` 和 `bind` 方法用于修改函数体内 `this` 指向

1.  `apply`、`call` 立即调用函数，返回函数的执行结果，其 `this` 值在执行时动态绑定（后续通过 `apply`、`call` 和 `bind` 修改 `this` 值有效），`bind` 不立即调用函数，而是返回一个新函数，需另外单独调用，其 `this` 值在绑定 `bind` 时绑定（不可重写，永久绑定，后续通过 `apply`、`call` 和 `bind` 修改 `this` 值均无效）
2.  `apply`：第二个参数为数组或类数组，可选
    `call`：参数列表，从第二个参数开始，依次传递给函数
    `bind`：从第二个参数开始的参数作为返回的新函数之参数
3.  性能方面 `call` > `apply` > `bind`：`call` 无需处理参数转换，逐个传参，函数直接执行，`apply` 需将参数依次传递给数组，再展开传递给函数，每次调用 `bind` 均创建一个新的函数对象，在调用时还需额外的执行上下文

```js
function fn() {
  console.log(this.x);
}

fn.bind({ x: 1 }).bind({ x: 2 })(); // 1
```