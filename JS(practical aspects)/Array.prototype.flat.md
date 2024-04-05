```JavaScript
Array.prototype._myflat = function (deep = 1) {
  let res = [];
  deep--;
  for (const p of this)
    Array.isArray(p) && deep >= 0
      ? (res = res.concat(p._myflat(deep)))
      : res.push(p);
  return res;
};

const arr = [1, [2, 3, [4, 5]], 1, 2, [6, 7]];
console.log(arr._myflat(1)); // [ 1, 2, 3, [ 4, 5 ], 1, 2, 6, 7 ]
```
