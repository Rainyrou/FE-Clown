```JavaScript
Array.prototype._flat = function (deep = 1) {
  let res = [];
  deep--;
  for (const p of this)
    Array.isArray(p) && deep >= 0
      ? (res = res.concat(p._flat(deep)))
      : res.push(p);
  return res;
};

const arr = [1, [2, 3, [4, 5]], 1, 2, [6, 7]];
console.log(arr._flat(1)); // [ 1, 2, 3, [ 4, 5 ], 1, 2, 6, 7 ]
```

```js
const flattenArray = (arr) => {
  let stk = [...arr];
  let res = [];
  while (stk.length) {
    let next = stk.pop();
    if (Array.isArray(next)) stk.push(...next);
    else res.push(next);
  }
  return res.reverse();
};

console.log(flattenArray([1, 2, 3, [4, 5], 1, 2, 6, 7]));
```
