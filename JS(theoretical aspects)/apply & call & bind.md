`apply`、`call` 和 `bind` 方法用于修改函数体内 `this` 指向

1. `apply`、`call` 立即调用函数，返回函数的执行结果，`bind` 不会立即调用函数，而是返回一个新函数，需单独调用，其 `this` 值在绑定 `bind` 时已指定
2. 
   `apply`：第二个参数为数组或类数组，可选
   `call`：参数列表，从第二个参数开始，依次传递给函数
   `bind`：从第二个参数开始的参数作为返回的新函数的参数