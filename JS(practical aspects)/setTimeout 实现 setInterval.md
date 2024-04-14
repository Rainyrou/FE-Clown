```JavaScript
function _setInterval(fn, delay = 4, ...args) {
  let cancel = false;
  const task = () => {
    setTimeout(() => {
      if (!cancel) {
        fn.apply(this, args);
        task();
      }
    }, delay);
  };
  task();
  return () => (cancel = true);
}
```

测试用例:

```JavaScript
const stopHello = _setInterval(() => console.log("Hello"), 1000);
setTimeout(() => stopHello(), 5000);
```

![[1697509242812.png]]

