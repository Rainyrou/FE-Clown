`for...of`：用于遍历可迭代对象的值，其调用对象的 `[Symbol.iterator]` 方法获取迭代器对象，再通过迭代器对象的 `next` 方法遍历所有返回值
`for...in`：用于遍历可迭代对象的可枚举字符串键，其不依赖于 ES6 的迭代器协议，遍历对象原型链上的可枚举字符串属性，通过 `hasOwnProperty` 方法过滤继承的属性
`Object.keys` 返回给定对象自身的所有可枚举字符串键之数组，不含位于原型链上的属性
1. 整数键 -> 按照升序排序
2. 字符串键 & Symbol 键 -> 按照创建顺序排序
获取 Symbol 键：
* `Object.getOwnPropertySymbols` 返回对象自身所有 `Symbol` 属性键
* `Reflect.ownKeys` 返回对象自身所有属性键（可枚举&不可枚举的字符串键 + `Symbol` 键）
`forEach`：返回 `undefined`，其无法通过 `break` 或 `return` 语句退出循环，因为其为高阶函数，对数组的每个元素执行一次提供的函数，以 for 循环、`for...of`、`for...in`、`some` 和 `every` 平替
`map` 对数组的每个元素执行一次提供的函数，返回包含应用该函数后的结果之新数组
就地改变数组的方法：`push` + `pop` + `unshift` + `shift` + `sort` + `reverse` + `fill` + `splice`
返回新数组的方法：`Array.from` + `new  Array` + `map` + `flatMap` + `filter` + `concat` + `slice`
