`Object.is` 判断两者是否为相同的值，其与 `==` 和 `===` 不同，它认为 NaN 与自身相等，且认为 +0 和 -0 不相等

```JavaScript
function _myobjectIs(x, y) {
    if(x === y) return x !== 0 || 1 / x === 1 / y;
    else return x !== x && y !== y;
};
```
