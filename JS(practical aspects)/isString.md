```js
const _isString = (value) =>
  typeof value === "string" || value instanceof String;

console.log(_isString("Hello World")); // true
console.log(_isString(123)); // false
console.log(_isString(new String("Hello World"))); // true
```