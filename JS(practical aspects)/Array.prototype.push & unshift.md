```JavaScript
Array.prototype._push = function (...elements) {
  if (this == null || typeof this.length !== "number")
    throw new TypeError(`${this} does not have a valid length property`);
  const startIndex = this.length;
  for (let i = 0; i < elements.length; ++i) this[startIndex + i] = elements[i];
  this.length = startIndex + elements.length;
  return this.length;
};
```

测试代码：

```JavaScript
const arr = [1, 2, 3];
console.log(arr._push(4, 5, 6)); // 6
console.log(arr); // [1, 2, 3, 4, 5, 6]
```

```JavaScript
Array.prototype._unshift = function (...elements) {
  if (this == null || typeof this.length !== "number")
    throw new TypeError(`${this} does not have a valid length property`);
  const startIndex = elements.length;
  for (let i = this.length - 1; i >= 0; --i) this[startIndex + i] = this[i];
  for (let i = 0; i < elements.length; ++i) this[i] = elements[i];
  return this.length;
};
```

当增加一个数组的索引超出其当前长度时，数组自动更新其 `length` 属性，无需显式增加 `this.length`

测试用例：

```JavaScript
const arr = [1, 2, 3];
console.log(arr._unshift(4, 5, 6));  // 6
console.log(arr);                      // [4, 5, 6, 1, 2, 3]
```