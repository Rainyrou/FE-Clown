在数组上实现 Reader 对象，可通过 getReader 函数获取 Reader 对象本身，且 Reader 对象上有 read（n）函数，每次调用按顺序读取数组的 n（默认为 1）个元素，调用不改变数组本身，若数组已全部读取完则返回空数组，若传入的参数不为正整数则抛出异常

```js
class ArrayReader {
  constructor(arr) {
    this.arr = arr;
    this.idx = idx;
  }
  getReader() {
    return this;
  }
  read(n = 1) {
    if (!Number.isInteger(n) || n <= 0)
      throw new Error("Parameter must be a positive integer");
    if (this.idx >= this.arr.length) return [];
    const res = this.arr.slice(this.idx, this.idx + n);
    this.idx += n;
    return res;
  }
}
```
