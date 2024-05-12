```JavaScript
Function.prototype._myapply = function (thisArg, args = []) {
  thisArg =
    thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
  const fn = Symbol();
  thisArg[fn] = this;
  const res = thisArg[fn](args);
  delete thisArg[fn];
  return res;
};
```

```JavaScript
Function.prototype._mycall = function (thisArg, ...args) {
  thisArg =
    thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
  const fn = Symbol();
  thisArg[fn] = this;
  const res = thisArg[fn](args);
  delete thisArg[fn];
  return res;
};
```

```JavaScript
Function.prototype._mybind = function (thisArg, ...args) {
  const _this = this;
  thisArg =
    thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
  return (...newArgs) => {
    const fn = Symbol();
    thisArg[fn] = _this;
    const res = thisArg[fn](...args, ...newArgs);
    delete thisArg[fn];
    return res;
  };
};
```
