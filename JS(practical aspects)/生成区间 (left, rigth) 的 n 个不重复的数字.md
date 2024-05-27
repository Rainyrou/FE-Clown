包括 left 和 right

```JavaScript
const generateUnique = (n, left, right) => {
  if (n > right - left + 1)
    throw new Error("Not enough unique numbers can be generated in the range.");
  const set = new Set();
  while (set.size < n) {
    const num = Math.floor(Math.random() * (right - left + 1) + left);
    set.add(num);
  }
  return [...set];
};
```
