```JavaScript
Array.prototype._mypush = function(...elements) {
    if(this == null || typeof this.length !== 'number') throw new TypeError(`${this} does not have a valid length property`);
    let startIndex = this.length;
    for(let i = 0;i < elements.length;++i) this[startIndex + i] = elements[i];
    this.length = startIndex + elements.length;
    // 返回新数组长度
    return this.length;
};
```

测试代码：

```JavaScript
let arr = [1, 2, 3];
console.log(arr._mypush(4, 5, 6)); // 6
console.log(arr); // [1, 2, 3, 4, 5, 6]
```