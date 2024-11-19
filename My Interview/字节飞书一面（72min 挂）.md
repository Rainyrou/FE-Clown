只记录不会的，面评没烂已是成功

1. Tree-Shaking 原理

2. async/defer 原理

3. 多端适配（除 TailwindCSS Perset + Rem 核心源码 + vw/vh + Media 查询外的方式）

4. 对象拍平

```js
const flattenObj = (obj, prefix = "") => {
  const ans = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      const newKey = prefix ? `${prefix}.${key}` : key;
      if (Array.isArray(obj[key])) {
        obj[key].forEach((item, index) => {
          const arrayKey = `${newKey}[${index}]`;
          ans[arrayKey] = item;
        });
      } else if (typeof obj[key] === "object" && obj[key] !== null) {
        Object.assign(ans, flattenObj(obj[key], newKey));
      } else {
        ans[newKey] = obj[key];
      }
    }
  }
  return ans;
};

const obj = {
  a: "hello",
  b: 111,
  c: {
    d: "world",
    e: [1, 2, 3],
  },
};

console.log(flattenObj(obj));
```
