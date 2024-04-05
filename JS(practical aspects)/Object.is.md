```JavaScript
function _myobjectIs(x, y) {
    if(x === y) return x !== 0 || 1 / x === 1 / y;
    else return x !== x && y !== y;
};
```

`Object.is` 方法判断两个值是否为相同的值。这与双等号 (`==`) 和三等号 (`===`) 有些不同，因为它认为 NaN 与自身相等，且认为 +0 和 -0 是不相等的

我们首先检查 `x` 是否不等于 0。如果不是 0，则直接返回 `true`。为什么我们这样做？因为 +0 和 -0 在 JavaScript 中使用三等号是相等的，但在 `Object.is` 规范中，它们被视为不同的值

我们通过计算 1 / x 和 1 / y 来区分 +0 和 -0。1 / +0 会返回 `Infinity`，而 1 / -0 会返回 `-Infinity`。所以，如果 x 和 y 都是 +0 或都是 -0，这个表达式会返回 `true`。但如果一个是 +0 而另一个是 -0，则会返回 `false`

接着 x 和 y 是否都是 `NaN`。在 JavaScript 中，`NaN` 不等于自己。因此会检查 x 是否是 `NaN` 且 y 是否也是 `NaN`。如果都是 `NaN`，则返回 `true`，否则返回 `false`
