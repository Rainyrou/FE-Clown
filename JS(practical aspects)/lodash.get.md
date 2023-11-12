`lodash.get` 允许我们安全地从对象中访问深层次的属性，即使某些中间属性可能不存在。这避免了当尝试访问不存在的属性时出现的 JavaScript 错误

- 将属性路径字符串分解为一个由键组成的数组
- 逐个遍历这些键，沿着对象的嵌套结构深入
- 如果在途中遇到 `undefined` 或非对象值，或者路径中的某个键不存在于当前对象中，那么直接返回 `undefined` 或指定的默认值。否则，返回路径末端的值

```JavaScript
function _get(object, key, value) {
  if (!Array.isArray(key)) {
    key = key
      .trim()
      .replace(/\[(.*)\]/g, (match, i) => {
        return `.${i}`;
      })
      .split(".");
  }

  for(let i = 0; i < key.length; ++i) {
    object = object[key[i]];
    if (!object) return value;
  }
  return object | value;
};
```

测试用例：

```JavaScript
var object = { 'a': [{ 'b': { 'c': 3 } }] };

console.log(_get(object, 'a[0].b.c'));
console.log(_get(object, ['a', '0', 'b', 'c']));
console.log(_get(object, 'a.b.c', 'default'));
```

正确的输出结果：

```
3
3
default
```

`_get` 函数有三个参数：

- `object`：需要获取值的对象
- `key`：表示对象路径的字符串或数组。字符串的形式可以是如 `'a.b.c'` 或 `'a[0].b.c'`
- `value`：如果指定的路径上的值是 `undefined`，则返回这个默认值。默认值是可选的

```JavaScript
if (!Array.isArray(key)) {
    key = key
      .trim()
      .replace(/\[(.*)\]/g, (match, i) => {
        return `.${i}`;
      })
      .split(".");
}
```

* 如果 `key` 不是数组，这部分代码将其处理为字符串形式，并将所有形如 `[value]` 的部分转换为 `.value`。这是为了处理字符串中的数组索引，例如将 `'a[0].b.c'` 转换为 `'a.0.b.c'`。然后使用 `split('.')` 方法将处理后的字符串转换为数组

- `\[` 和 `\]` 是匹配左右方括号的，因为 `[` 和 `]` 在正则中有特殊意义，因此需要用 `\` 来转义
- `(.*)`：这是一个捕获组，`.` 匹配任何字符，`*` 表示匹配 0 或多个。所以这个捕获组会匹配方括号中的所有内容
- `(match, i) => { ... }`：这是 `replace` 方法的第二个参数，一个回调函数。当正则匹配到一个子串时，这个函数会被调用
    - `match`：这是完整的匹配结果，例如，对于字符串 `"a[0].b"`, `match` 会是 `[0]`
    - `i`：这是第一个捕获组的结果。因为我们的正则中只有一个捕获组，所以 `i` 会得到括号内的内容。对于字符串 `"a[0].b"`, `i` 会是 `0`

```JavaScript
for(let i = 0; i < key.length; ++i) {
    object = object[key[i]];
    if (!object) return value;
}
```

这部分代码遍历上面处理得到的路径数组，然后依次获取对象中的值。如果在某个步骤中，获取到的值为 `falsy`（如 `null`、`undefined`、`0`），则直接返回默认值 `value`。否则，循环结束后返回获取到的值或默认值

