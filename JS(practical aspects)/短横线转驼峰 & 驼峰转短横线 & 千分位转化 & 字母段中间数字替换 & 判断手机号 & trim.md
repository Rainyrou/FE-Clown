短横线转驼峰：

- `all` 即整个匹配到的字符串如输入为 `"background-color"`，则 `all` 为 `-c`
- `match` 即匹配到的捕获组中之字符如输入为 `"background-color"`，则 `match` 为 `c`

```JavaScript
const toCamelCase = (str) =>
  str.replace(/-([a-z])/g, (all, match) => match.toUpperCase());
```

驼峰转短横线：

```JavaScript
const toKebabCase = (str) =>
  str
    .replace(/([a-z])([A-Z])/g, (all, match1, match2) => `${match1}-${match2}`)
    .toLowerCase();
```

千分位转化：

* 正则：

```js
const formatToThousands = (num) => {
  const item = num.toString().split(".");
  item[0] = item[0].replace(/(\d)(?=(\d{3})+(?!\d))/g, "$1,");
  return `${item[0]}.${item[1]}`;
};

console.log(formatToThousands(123456789.123456789));
```

* 通解：

```js
const formatToThousands = (num) => {
  const item = num.toString().split(".");
  const reversed = item[0].split("").reverse();
  const chunks = [];
  for (let i = 0; i < reversed.length; i += 3)
    chunks.push(reversed.slice(i, i + 3).join(""));
  const formatted = chunks.join(",").split("").reverse().join("");
  return `${formatted}.${item[1]}`;
};

console.log(formatToThousands(123456789.123456789));
```

字母段中间数字替换：

```js
const convert = (str) => {
  return str.replace(/([a-zA-Z]+)/g, (all, match) => {
    const len = match.length;
    if (len > 2) return match[0] + (len - 2) + match[len - 1];
    return match;
  });
};

console.log(convert("--_abcd_--")); // --_a2d_--
console.log(convert("--_cd_--")); // --_cd_--
console.log(convert("_-abcd-__-efg-__-hijkl-_")); // _-a2d-__-e1g-__-h3l-_
```

判断手机号：

```js
const isPhone = (phone) => /^1[3-9]\d{9}$/.test(phone);
```

trim：

```js
const trim = (str) => str.replace(/^\s+|\s+$/g, "");
console.log(trim(" \n\t Hello World \r\n ")); // Hello World
```