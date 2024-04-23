```JavaScript
const _get = (obj, key, val) => {
  if (!Array.isArray(key)) {
    key = key
      .trim()
      .replace(/\[(.*)\]/g, (match, i) => `.${i}`)
      .split(".");
  }
  for (let i = 0; i < key.length; ++i) {
    obj = obj[key[i]];
    if (!obj) return val;
  }
  return obj || val;
};
```

测试用例：

```JavaScript
var object = { 'a': [{ 'b': { 'c': 3 } }] };
console.log(_get(object, 'a[0].b.c')); // 3
console.log(_get(object, ['a', '0', 'b', 'c'])); // 3
console.log(_get(object, 'a.b.c', 'default')); // default
```

