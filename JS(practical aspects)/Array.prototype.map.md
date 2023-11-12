```JavaScript
Array.prototype._mymap = function(callback, thisBinding = globalThis) {
    if(typeof callback !== 'function') {
        throw new TypeError(`${callback} is not a function`);
    }
    if(this == null || typeof this[Symbol.iterator] !== 'function') {
        throw new TypeError(`${this} is not a iterator`);
    }
    const res = [];
    const arr = [...this];
    for(let i = 0;i < arr.length;++i) {
        res.push(callback.call(thisBinding, arr[i], i, this));
    }
    return res;
};
```

