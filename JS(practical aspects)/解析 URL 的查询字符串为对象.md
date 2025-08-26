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
