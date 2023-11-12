```JavaScript
const arr = [1, [2, 3, [4, 5]], 1, 2, [6, 7]];

Array.prototype._myflat = function(deep = 1) {
    let res = [];
    deep--;
    // `_myflat` 是作为当前数组的一个方法被调用，`this` 会指向该数组
    for(const p of this) {
        if(Array.isArray(p) && deep >= 0) {
            res = res.concat(p._myflat(deep));
        } else {
            res.push(p);
        }
    }
    return res;
};

console.log(arr._myflat(1));
```

正确的输出结果

```
[ 1, 2, 3, [ 4, 5 ], 1, 2, 6, 7 ]
```