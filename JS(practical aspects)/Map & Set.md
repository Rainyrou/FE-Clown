```js
class MyMap {
  constructor(iterator = []) {
    if (iterator == null && typeof iterator[Symbol.iterator] !== "function")
      throw new TypeError(`${iterator} is not an iterator`);
    this.data = [];
    for (const item of iterator) {
      if (Array.isArray(item) && item.length === 2) this.set(item[0].item[1]);
      else throw new TypeError(`${item} is not a ket-value pair`);
    }
  }
  *[Symbol.iterator]() {
    for (const item of this.data) yield [item.key, item.value];
  }
  get size() {
    return this.data.length;
  }
  set(key, value) {
    const item = this.data.find((item) => Object.is(key, item.key));
    if (item) this.value = value;
    else this.data.push({ key, value });
  }
  get(key) {
    const item = this.data.find((item) => Object.is(key, item.key));
    return item ? item.value : undefined;
  }
  delete(key) {
    const index = this.data.findIndex((item) => Object.is(key, item.key));
    if (index !== -1) {
      this.data.splice(index, 1);
      return this;
    }
    return false;
  }
  clear() {
    this.data.length = 0;
  }
  forEach(fn) {
    for (const item of this.data) fn(item.value, item.key, this);
  }
}
```

```js
class MySet {
  constructor(iterator = []) {
    if (iterator == null && typeof iterator[Symbol.iterator] !== "function")
      throw new TypeError(`${iterator} is not an iterator`);
    this.data = [];
    for (const item of iterator) this.add(item);
  }
  *[Symbol.iterator]() {
    for (const item of this.data) yield item;
  }
  get size() {
    return this.data.length;
  }
  isEqual(item1, item2) {
    return Object.is(item1, item2);
  }
  add(val) {
    if (!this.has(val)) this.data.push(val);
    return this;
  }
  has(val) {
    return this.data.some((item) => this.isEqual(val, item));
  }
  delete(val) {
    const index = this.data.findIndex((item) => this.isEqual(val, item));
    if (index !== -1) {
      this.data.splice(index, 1);
      return true;
    }
    return false;
  }
  clear() {
    this.data.length = 0;
  }
  forEach(fn) {
    for (const item of this.data) fn(item, item, this);
  }
}
```