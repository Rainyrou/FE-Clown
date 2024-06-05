```js
const parseQuery = (url) => {
  let queryString = "";
  const urlParts = url.split("?");
  if (urlParts.length > 1) queryString = urlParts[1].split("#")[0];
  else if (url.startsWith("?")) queryString = url.slice(1);
  const obj = {};
  if (!queryString) return obj;
  queryString.split("&").forEach((part) => {
    const item = part.split("=");
    const key = decodeURIComponent(item[0]),
      val = decodeURIComponent(item[1] ? item[1].replace(/\+/g, " ") : "");
    if (obj.hasOwnProperty(key)) {
      if (Array.isArray(obj[key])) obj[key].push(val);
      else obj[key] = [obj[key], val];
    } else {
      obj[key] = val;
    }
  });
  return obj;
};
```