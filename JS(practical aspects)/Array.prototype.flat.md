```JavaScript
const flattenArray = (arr) => {
  const ans = [];
  const flatten = (arr) => {
    for (const item of arr) {
      if (Array.isArray(item)) flatten(item);
      else ans.push(item);
    }
  };
  flatten(arr);
  return ans;
};

console.log(flattenArray([1, 2, 3, [4, [5]], 1, 2, 6, 7]));
```

```js
const flattenArray = (arr) => {
  const stk = [...arr],
    ans = [];
  while (stk.length) {
    const next = stk.pop();
    if (Array.isArray(next)) stk.push(...next);
    else ans.unshift(next);
  }
  return ans;
};

console.log(flattenArray([1, 2, 3, [4, [5]], 1, 2, 6, 7]));
```
