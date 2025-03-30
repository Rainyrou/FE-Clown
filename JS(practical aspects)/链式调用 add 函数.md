```js
function add(...args) {
  const sum = args.reduce((a, b) => a + b, 0);
  return function (...newArgs) {
    return newArgs.length ? add(sum, ...newArgs) : sum;
  };
}

console.log(add(1, 2, 3)(4, 5)(6)()); // 21
```

```js
function sum(...args) {
  const allArgs = [...args];
  const fn = (...newArgs) => {
    allArgs.push(...newArgs);
    return fn;
  };
  fn.sumoff = () => allArgs.reduce((a, b) => a + b, 0);
  return fn;
}

console.log(sum(1, 2).sumoff()); // 3
console.log(sum(1, 2)(3).sumoff()); // 6
console.log(sum(1)(2)(3)(4).sumoff()); // 10
```


