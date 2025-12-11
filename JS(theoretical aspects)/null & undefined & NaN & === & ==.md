* 空值 -> `null`
* 变量未初始化 + 函数无返回值 -> `undefined` 
- `NaN` 非数字，其表示数学运算返回值无效
- `==`：宽松相等，若 JavaScript 对象的数据类型不同，则隐式转换后再比较
- `===`：严格相等，若 JavaScript 对象的数据类型不同，返回 `false`

```js
console.log(null === undefined); // false
console.log(null == undefined); // true
console.log(NaN === NaN); // false
console.log(NaN == NaN); // false
console.log({} === {}); // false
console.log({} == {}); // false
console.log([] === []); // false
console.log([] == []); // false
console.log(1 + "2"); // "12"
console.log(1 - "2"); // -1
console.log(typeof null); // object
console.log(typeof undefined); // undefined
console.log(typeof NaN); // number
```
