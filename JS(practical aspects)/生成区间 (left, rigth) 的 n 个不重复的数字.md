```JavaScript
const generateUnique = (n, left, right) => {
  if (n > right - left - 1)
    throw new Error("Not enough unique numbers can be generated in the range.");
  const res = new Set();
  while (res.size < n) {
    const number = Math.floor(Math.random() * (right - left - 1) + left + 1);
    res.add(number);
  }
  return [...res];
};
```

