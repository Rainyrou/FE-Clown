```js
const _get = (obj, key, val) => {
  if (!Array.isArray(key))
    key = key
      .trim()
      .replace(/\[(.*)\]/g, (all, match) => `.${match}`)
      .split(".");
  for (const item of key) {
    obj = obj[item];
    if (!obj) return val;
  }
  return obj || val;
};
```

测试用例：

```js
var object = { 'a': [{ 'b': { 'c': 3 } }] };
console.log(_get(object, 'a[0].b.c')); // 3
console.log(_get(object, ['a', '0', 'b', 'c'])); // 3
console.log(_get(object, 'a.b.c', 'default')); // default
```

变式：

```js
const _get = (obj, path) =>
  path
    .replace(/\[(\d+)\]/g, ".$1")
    .split(".")
    .reduce((o, key) => (o && o[key] !== undefined ? o[key] : undefined), obj);

console.log(_get({ a: { b: [2] }, c: 1 }, "a.b[0]")); // 2
console.log(_get({ a: { b: [2] }, c: 1 }, "c")); // 1
console.log(_get({ x: { y: { z: 10 } } }, "x.y.z")); // 10
console.log(_get({ a: [{ b: 3 }] }, "a[0].b")); // 3
console.log(_get({ a: { b: [2] } }, "a.b[1]")); // undefined
console.log(_get({}, "a.b.c")); // undefined
```


