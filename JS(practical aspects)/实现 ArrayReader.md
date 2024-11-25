为 Array 实现一个 Reader，通过接口 getReader 获取，Reader 有一个接口 read（n），每次调用按顺序读取数组的 n（默认为 1）个元素，调用不会改变数组本身的值，若数组已全部读取完则返回空数组，若传入的参数不为正整数则抛出异常

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
