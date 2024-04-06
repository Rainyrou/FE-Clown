```JavaScript
Array.prototype._myincludes = function(searchElement, fromIndex = 0) {
    if(this == null || typeof this[Symbol.iterator] !== 'function') throw new TypeError(`${this} is not an iterator`);
    fromIndex = Math.max(fromIndex, 0);
    for(let i = fromIndex;i < this.length;++i) {
        if(this[i] === searchElement || (isNaN(searchElement) && isNaN(this[i]))) return true;
    }
    return false;
};
```

测试代码

```JavaScript
let arr = [1, 2, 3, NaN];
console.log(arr._myincludes(2));       // true
console.log(arr._myincludes(4));       // false
console.log(arr._myincludes(NaN));     // true
```



