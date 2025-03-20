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

若 `obj[decodedKey]` 存在，`[].concat` 将 `obj[decodedKey]` 的值与 `decodedVal` 合并成一个新数组：  
* 如果 `obj[decodedKey]` 为一个值如字符串 `"Alice"`，则变成数组如 `[].concat("Alice", "Bob")` 会得到 `["Alice", "Bob"]`
- 如果 `obj[decodedKey]` 已为数组，则直接添加 `decodedVal` 
若 `obj[decodedKey]` 不存在，直接将 `decodedVal` 赋值给 `obj[decodedKey]`，无需合并数组
