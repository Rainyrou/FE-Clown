```js
Array.prototype._myReverse = function () {
  if (this == null || typeof this[Symbol.iterator] !== "function")
    throw new TypeError(`${this} is not an iterator`);
  const arr = [...this];
  for (let left = 0, right = arr.length - 1; left < right; ++left, --right)
    [arr[left], arr[right]] = [arr[right], arr[left]];
  for (let i = 0; i < arr.length; ++i) this[i] = arr[i];
  return this;
};
```

测试用例：

```js
const numbers = [1, 2, 3, 4, 5];
console.log(numbers._myReverse()); // [ 5, 4, 3, 2, 1 ]
```