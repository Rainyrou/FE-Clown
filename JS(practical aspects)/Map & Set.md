```js
class MyMap {
  constructor() {
    this.data = [];
  }
  size() {
    return this.data.length;
  }
  get(key) {
    const item = this.data.find((item) => Object.is(key, item.key));
    return item ? item.value : undefined;
  }
  set(key, value) {
    const item = this.data.find((item) => Object.is(key, item.key));
    if (item) item.value = value;
    else this.data.push({ key, value });
  }
  delete(key) {
    const index = this.data.findIndex((item) => Object.is(key, item.key));
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
    for (const item of this.data) fn(item.value, item.key, this);
  }
}
```

```js
class MySet {
  constructor() {
    this.data = [];
  }
  size() {
    return this.data.length;
  }
  has(val) {
    return this.data.includes(val);
  }
  add(val) {
    const index = this.data.indexOf(val);
    if (index === -1) this.data.push(val);
  }
  delete(val) {
    const index = this.data.indexOf(val);
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
    for (const val of this.data) fn(val);
  }
}
```