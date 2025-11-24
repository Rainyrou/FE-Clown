数组乱序：

* `Math.random` + `sort` -> 非均匀分布 + 性能问题

```JavaScript
const shuffle = (nums) => nums.sort(() => Math.random() - 0.5);
```

* Fisher-Yates/Knuth 洗牌算法 -> 时间复杂度 O(n)

```js
const shuffle = (nums) => {
  for (let i = nums.length - 1; i > 0; --i) {
    const j = Math.floor(Math.random() * (i + 1));
    [nums[i], nums[j]] = [nums[j], nums[i]];
  }
  return nums;
};
```

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
