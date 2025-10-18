```js
const parseQuery = (url) => {
  const queryString = url.includes("?") ? url.split("?")[1].split("#")[0] : "";
  const obj = {};
  if (!queryString) return obj;
  queryString.split("&").forEach((part) => {
    const [key, val = ""] = part.split("=");
    const decodedKey = decodeURIComponent(key);
    const decodedVal = decodeURIComponent(val.replace(/\+/g, " "));
    obj[decodedKey] = obj[decodedKey]
      ? [].concat(obj[decodedKey], decodedVal)
      : decodedVal;
  });
  return obj;
};
```

若 `obj[decodedKey]` 存在，`[].concat` 将 `obj[decodedKey]` 的值与 `decodedVal` 合并为一个新数组：  
* 若 `obj[decodedKey]` 不为数组如字符串 `"Alice"`，则转化为数组如 `[].concat("Alice", "Bob")` 的结果为 `["Alice", "Bob"]`
- 若 `obj[decodedKey]` 为数组，则直接添加 `decodedVal` 
若 `obj[decodedKey]` 不存在，直接将 `decodedVal` 赋值给 `obj[decodedKey]`，无需合并数组

```js
const parseQueryString = (queryString) => {
  const obj = {};
  if (!queryString) return obj;
  queryString.split("&").forEach((part) => {
    const [key, val = ""] = part.split("=");
    const decodedKey = decodeURIComponent(key);
    const decodedVal = decodeURIComponent(val.replace(/\+/g, " "));
    obj[decodedKey] = obj[decodedKey]
      ? [].concat(obj[decodedKey], decodedVal)
      : decodedVal;
  });
  return obj;
};

const parseUrl = (urlString) => {
  const regex =
    /^([a-z]+):\/\/([^:/]+)(?::(\d+))?(?:[^?#]*)?(?:\?([^#]*))?(?:#(.*))?$/i;
  const matches = urlString.match(regex);
  const rawQuery = matches[4] || "";
  const query = parseQueryString(rawQuery);
  return {
    protocol: matches[1] || null,
    hostname: matches[2] || null,
    port: matches[3] || null,
    query: query,
    hash: matches[5] || null,
  };
};

console.log(
  parseUrl(
    "https://www.example.com:8080/path/to/page?user=test&id=123&id=456&q=a+b#section1"
  )
);
```

非捕获组 `(?:...)` 通过括号进行逻辑分组，但不将其计入最终的 `matches` 结果数组中

| 符号  | 位置        | **含义**  | 示例       |
| --- | --------- | ------- | -------- |
| `^` | 正则最开头     | 字符串起始位置 | `^abc`   |
| `^` | 方括号 `[` 后 | 匹配非 X   | `[^abc]` |
