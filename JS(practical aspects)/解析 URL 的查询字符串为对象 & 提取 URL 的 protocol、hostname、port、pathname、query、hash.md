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

`match` 方法的参数为不含 `g` 修饰符的正则表达式时，其返回数组，数组首元素为与该正则表达式模式相匹配的完整字符串，其他元素为与正则表达式中通过括号定义的捕获组匹配的子字符串

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

const parseUrl = (url) => {
  const result = {
    protocol: null,
    hostname: null,
    port: null,
    pathname: "/",
    query: {},
    hash: "",
  };

  const protocolMatch = url.match(/^([a-z]+):\/\//);
  if (protocolMatch) {
    result.protocol = protocolMatch[1];
    let rest = url.slice(protocolMatch[0].length);
    
    const hashIndex = res.indexOf("#");
    if (hashIndex !== -1) {
      result.hash = res.slice(hashIndex + 1);
      res = res.slice(0, hashIndex);
    }

    const queryIndex = res.indexOf("?");
    if (queryIndex !== -1) {
      const queryString = res.slice(queryIndex + 1);
      result.query = parseQueryString(queryString);
      res = res.slice(0, queryIndex);
    }

    const parts = res.split("/"),
      hostPortPart = parts[0];
    if (parts.length > 1) result.pathname = "/" + parts.slice(1).join("/");
    if (hostPortPart) {
      const [hostname, portStr] = hostPortPart.split(":");
      result.hostname = hostname;
      result.port = portStr ? Number(portStr) : null;
    }
  }
  return result;
};

console.log(
  parseUrl(
    "https://www.example.com:8080/path/to/page?user=test&id=123&id=456&q=a+b#section1"
  )
);
```