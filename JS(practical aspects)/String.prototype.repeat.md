```js
String.prototype._repeat = function (n) {
  if (n < 0) throw new RangeError(`${n} must be non-negative`);
  let repeatStr = "";
  for (let i = 0; i < n; ++i) repeatStr += this;
  return repeatStr;
};
```