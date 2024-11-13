```js
function add(...args) {
  const sum = args.reduce((a, b) => a + b, 0);
  return function (...newArgs) {
    return newArgs.length ? add(sum, ...newArgs) : sum;
  };
}

console.log(add(1, 2, 3)(4, 5)(6)()); // 21
```