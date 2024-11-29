```js
const sqrtNewton = (n, epsilion = 1e-7) => {
  if (n === 0) return 0;
  let x = n;
  while (true) {
    const nextX = 0.5 * (x + n / x);
    if (Math.abs(nextX - x) < epsilion) return nextX;
    x = nextX;
  }
};

console.log(sqrtNewton(25)); // 5
console.log(sqrtNewton(2)); // 1.414213562373095
console.log(sqrtNewton(0)); // 0
console.log(sqrtNewton(0.01)); // 0.1
```