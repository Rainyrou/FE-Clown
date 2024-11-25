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
  const stk = [...arr],
    ans = [];
  while (stk.length) {
    const next = stk.pop();
    if (Array.isArray(next)) stk.push(...next);
    else ans.push(next);
  }
  return ans.reverse();
};

console.log(flattenArray([1, 2, 3, [4, [5]], 1, 2, 6, 7]));
```
