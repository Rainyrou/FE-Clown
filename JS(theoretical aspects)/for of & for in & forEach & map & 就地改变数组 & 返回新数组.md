`for...of`：用于遍历可迭代对象的值如数组类、数组、字符串、Map、Set 等，其调用对象的 `[Symbol.iterator]` 方法获取迭代器对象，接着通过迭代器对象的 `next` 方法遍历所有返回值
`for...in`：用于遍历可迭代对象的可枚举属性如对象和数组，其不依赖于 ES6 的迭代器协议，还遍历对象原型链上的可枚举属性，可通过 `hasOwnProperty` 方法过滤掉继承的属性
`forEach` 对数组的每个元素执行一次提供的函数，返回 `undefined`
`map` 对数组的每个元素执行一次提供的函数，返回包含应用该函数后的结果之新数组
就地改变数组的方法：`push` + `pop` + `unshift` + `shift` + `splice` + `sort` + `reverse` + `fill`
返回新数组的方法：`map` + `flatMap` + `filter` + `concat` + `slice` + `from`
