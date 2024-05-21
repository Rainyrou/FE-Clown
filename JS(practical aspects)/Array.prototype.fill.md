```JavaScript
Array.prototype._fill = function(value, start = 0, end = this.length) {
    if(this == null || typeof this[Symbol.iterator] !== 'function') throw new TypeError(`${this} is not a iterator`);
    start = Math.max(Math.min(start, this.length), 0);
    end = Math.max(Math.min(end, this.length), 0);
    // 原生方法直接修改调用它的数组或类数组
    for(let i = start;i < end;++i) this[i] = value;
    // 原生方法就地改变数组或类数组
    return this;
};
```