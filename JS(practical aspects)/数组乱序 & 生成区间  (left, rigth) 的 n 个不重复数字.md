数组乱序：

1. `Math.random` + `sort`

```JavaScript
const shuffle = (arr) => arr.sort(() => Math.random() - 0.5);
```

非均匀分布 + 性能问题

2. Fisher-Yates/Knuth 洗牌算法：

```js
const shuffle = (arr) => {
  for (let i = arr.length - 1; i > 0; --i) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
  return arr;
};
```

时间复杂度 O(n)

原地交换，每一步交换均在未处理区间内随机选择以保证洗牌结果完全随机，所有元素重排且只出现一次

生成区间 (left, rigth) 的 n 个不重复数字，包括 left 和 right：

```JavaScript
const generateUnique = (n, left, right) => {
  const set = new Set();
  while (set.size < n) {
    const num = Math.floor(Math.random() * (right - left + 1) + left);
    set.add(num);
  }
  return [...set];
};
```
