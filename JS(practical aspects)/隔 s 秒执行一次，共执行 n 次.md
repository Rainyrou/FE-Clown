```js
function repeatFunction(fn, s, n) {
  let count = 0;
  const interval = setInterval(() => {
    if (count < n) {
      fn();
      ++count;
    } else {
      clearInterval(interval);
    }
  }, s * 1000);
}

repeatFunction(() => console.log("Hello"), 2, 5);
```