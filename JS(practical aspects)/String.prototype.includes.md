```js
String.prototype._includes = function (word) {
  if (word === "") return true;
  for (let i = 0; i < this.length - word.length + 1; ++i) {
    let subStr = "";
    for (let j = i; j < i + word.length; ++j) subStr += this[j];
    if (subStr === word) return true;
  }
  return false;
};
console.log("Hello world"._includes("Hello ")); // true
```