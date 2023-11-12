1. 定义：

   - `undefined`：当一个变量被声明但尚未赋值时，默认值为 `undefined`
   - `null`：当一个变量被赋值为 `null` 时，该变量指向了一个空对象或没有对象

2. 类型：

   - `undefined` 是自己的数据类型，`typeof undefined` 返回 `undefined`
   - `null` 虽然是一个对象的空引用，但由于历史原因，`typeof null` 返回 `object`

3. 出现场景：

   - 当你尝试访问一个不存在的对象属性或一个未初始化的变量或一个函数没有返回任何值，你会得到 `undefined`
   - 当你想明确表示某个值是空或不存在时，通常会使用 `null`

4. 相等性比较：

   - `null == undefined` 为 `true`
   - `null === undefined` 为 `false`，因为它们是不同的数据类型


- `==`（双等号）：称为"宽松相等"操作符，如果两个值的类型不同，JavaScript 会尝试转换它们的数据类型，然后进行比较
- `===`（三等号）：称为"严格等于"操作符，如果两个值的类型不同，直接返回 `false`
- `NaN === NaN` 为 `false`
- 为了检测一个值是否是 `NaN`，推荐使用 ES6 提供的 `Number.isNaN()` 方法
- `NaN` 不是数字，它表示一个数学运算没有返回有效的结果，但 `typeof NaN` 返回 `"number"`




