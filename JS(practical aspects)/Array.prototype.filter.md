```JavaScript
Array.prototype._myFillter = function (callback, thisBinding = globalThis) {
  if (typeof callback !== "function")
    throw new TypeError(`${callback} is not a function`);
  if (this == null || typeof this[Symbol.iterator] !== "function")
    throw new TypeError(`${this} is not an iterator`);
  const arr = [...this];
  const res = [];
  for (let i = 0; i < arr.length; ++i) if (callback.call(thisBinding, arr[i], i, this)) res.push(arr[i]);
  return res;
};
```

测试用例：

```JavaScript
const numbers = [1, 2, 3, 4, 5];
const evenNumbers = numbers._myFillter((number) => number % 2 === 0);
console.log(evenNumbers); // [2, 4]
```

