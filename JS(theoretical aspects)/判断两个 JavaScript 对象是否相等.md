1. 浅比较：只比较 JavaScript 对象的第一层属性值，而不递归比较深层嵌套对象
2. 深比较：递归比较 JavaScript 对象所有层级的类型及属性值，特殊处理循环引用和日期、正则和 TypedArray 等 JavaScript 对象等
3. JSON.stringify：比较可序列化的 JavaScript 对象
4. Lodash.isEqual：覆盖所有极端边界情况如循环引用和日期、正则、TypedArray 等 JavaScript 对象
