普通函数的 `arguments` 是一个类数组，包含函数调用时传入的所有参数，通过索引访问各个参数且有一个表示参数数量的 `length` 属性，可通过`[...arguments]` 或 `Array.prototype.slice.call(arguments)` 将其转化为数组

