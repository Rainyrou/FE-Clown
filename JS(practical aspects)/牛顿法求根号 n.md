```js
const sqrtNewton = (n) => {
  if (n === 0) return 0;
  let x = n;
  while (true) {
    const nextX = 0.5 * (x + n / x);
    if (Math.abs(nextX - x) < 1e-7) return nextX;
    x = nextX;
  }
};

console.log(sqrtNewton(25)); // 5
console.log(sqrtNewton(2)); // 1.414213562373095
console.log(sqrtNewton(0)); // 0
console.log(sqrtNewton(0.01)); // 0.1
```