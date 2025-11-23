- ID/Class -> 字母 + 数字 + 下划线 + 连字符
- Pseudo-class/Pseudo-element -> 字母 + 连字符
- `match` 返回最小 CSS 选择器单元数组，扫描 `selector` 字符串，提取与正则表达式中任一模式相匹配的子串并置于结果数组

```js
const calculate = (selector) => {
  let id = 0,
    cls = 0,
    element = 0;
  const matched = selector.match(
    /(::[a-zA-Z-]+|:[a-zA-Z-]+|#[\w-]+|\.[\w-]+|\[(.*?)\]|[a-zA-Z][\w-]*)/g
  );
  for (const item of matched) {
    if (item.startsWith("#")) id += 100;
    else if (item.startsWith(".")) cls += 10;
    else if (item.startsWith("[")) cls += 10;
    else if (item.startsWith("::")) element += 1;
    else if (item.startsWith(":")) cls += 10;
    else element += 1;
  }
  return id + cls + element;
};

const compare = (selector1, selector2) => {
  const weight1 = calculate(selector1),
    weight2 = calculate(selector2);
  if (weight1 > weight2) return 1;
  else if (weight1 < weight2) return -1;
  else return 0;
};

console.log(calculate("div")); // 1
console.log(calculate(".cls")); // 10
console.log(calculate("#id")); // 100
console.log(calculate("div.cls")); // 11
console.log(calculate("div:hover")); // 11
console.log(calculate("div::before")); // 2
console.log(calculate('[type="text"]')); // 10

console.log(compare("#id", ".class")); // 100 vs 10 -> 1
console.log(compare(".class1.class2", "#id")); // 20 vs 100 -> -1
console.log(compare("div#id", "#id")); // 101 vs 100 -> 1
console.log(compare(".class:nth-child(2)", "#id")); // 20 vs 100 -> -1
console.log(compare("div.class", ".class")); // 11 vs 10 -> 1
console.log(compare("div", "span")); // 1 vs 1 -> 0
```
