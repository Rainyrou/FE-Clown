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

在 JS 中，当你增加一个数组的索引超出其当前长度时，它会自动更新该数组的 `length` 属性。因此没有必要显式地增加`this.length`，因为在移动数组元素时已自动完成

测试代码：

```JavaScript
let arr = [1, 2, 3];
console.log(arr._myunshift(4, 5, 6));  // 6
console.log(arr);                      // [4, 5, 6, 1, 2, 3]
```
