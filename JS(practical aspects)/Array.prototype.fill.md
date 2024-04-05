```JavaScript
Array.prototype._myfill = function(value, start = 0, end = this.length) {
    if(this == null || typeof this[Symbol.iterator] !== 'function') {
        throw new TypeError(`${this} is not a iterator`);
    }
    start = Math.max(Math.min(start, this.length), 0);
    end = Math.max(Math.min(end, this.length), 0);
    // 原生 `Array.prototype.fill` 直接修改调用它的数组
    // `Array.prototype.fill` 也可工作于类数组对象
    for(let i = start;i < end;++i) {
        this[i] = value;
    }
    // 原生 `fill` 方法返回修改后的原始数组或类数组对象，而不是一个新的数组或类数组对象
    return this;
};
```