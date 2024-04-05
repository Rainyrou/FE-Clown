```JavaScript
Array.prototype._myunshift = function(...elements) {
    if(this == null || typeof this.length !== 'number') {
        throw new TypeError(`${this} dose not have a valid length property`);
    }
    const addCount = elements.length;
    for(let i = this.length - 1;i >= 0;--i) {
        this[i + addCount] = this[i];
    }
    for(let i = 0;i < elements.length;++i) {
        this[i] = elements[i];
    }
    // 返回新数组的长度
    return this.length;
};
```

当增加一个数组的索引超出其当前长度时，数组自动更新其 `length` 属性，无需显式增加 `this.length`

测试代码：

```JavaScript
let arr = [1, 2, 3];
console.log(arr._myunshift(4, 5, 6));  // 6
console.log(arr);                      // [4, 5, 6, 1, 2, 3]
```
