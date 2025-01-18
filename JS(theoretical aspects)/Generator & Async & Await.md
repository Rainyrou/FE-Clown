Generator 即 ES6 关于协程的实现，其为一个封装的异步任务，以 `yield` 暂停执行函数，函数名前加星号。调用它不会立即执行函数体中的代码，而是返回指针对象。Generator 对象是一个迭代器对象，其 `next` 方法用于遍历函数内部的 `yield` 表达式，即分阶段执行 Generator 函数，每次调用 `next` 方法，均返回一个表示当前阶段信息的对象。`value` 是 `yield` 语句后表达式的值，表示当前阶段的值；`done` 为布尔值，表示函数是否执行完，`next` 方法返回的 `value` 是 Generator 函数的输出数据，`next` 方法接受的参数是外部向 Generator 函数的输入数据

Async/Await 在实现上结合 Generator 与 Promise，简化使用多个 Promise 时的同步行为，无需关心某个操作是否为异步操作
