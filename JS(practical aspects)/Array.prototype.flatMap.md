```js
Array.prototype._flatMap = function (callback, thisArg) {
  const res = [];
  this.forEach((item, index, arr) => {
    const mappedValue = callback.call(thisArg, item, index, arr);
    if (Array.isArray(mappedValue)) res.push(...mappedValue);
    else res.push(mappedValue);
  });
  return res;
};
```

测试用例：

```js
const arr = [1, 2, 3, 4];
const res = arr._flatMap((x) => [x, x * 2]);
console.log(res); // [1, 2, 2, 4, 3, 6, 4, 8]
```