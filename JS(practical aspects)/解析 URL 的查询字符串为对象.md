1. `URLSearchParams`

```js
const parseQuery = (queryString) => {
  const params = new URLSearchParams(queryString);
  const obj = {};
  for (const [key, value] of params) obj[key] = value;
  return obj;
};

const queryObj = parseQuery(window.location.search);
console.log(queryObj);
``` 

![[1712328627732.png]]

2. 手写

```js
const parseQuery = (queryString) => {
  const pairs = queryString[0] === "?" ? queryString.slice(1) : queryString;
  const obj = {};
  pairs.split("&").forEach((part) => {
    const item = part.split("=");
    obj[decodeURIComponent(item[0])] = decodeURIComponent(
      item[1] ? item[1] : ""
    ).replace(/\+/g, " ");
  });
  return obj;
};

const queryObj = parseQuery(window.location.search);
console.log(queryObj);
```

![[1712331329759.png]]