`toString(16)` 将十进制数字转换为十六进制字符串

```js
const range = (num) => num >= 0 && num <= 255;

const rgb2hex = (r, g, b) => {
  if (!range(r) || !range(g) || !range(b)) return;
  let rh = r.toString(16),
    gh = g.toString(16),
    bh = b.toString(16);
  rh = rh.length === 1 ? "0" + rh : rh;
  gh = gh.length === 1 ? "0" + gh : gh;
  bh = bh.length === 1 ? "0" + bh : bh;
  return "#" + rh + gh + bh;
};
```

`parseInt(..., 16)` 将十六进制字符串转换为十进制数字

```js
const hex2Rgb = (hex) => {
  let hexStr = hex.startsWith("#") ? hex.slice(1) : hex;
  if (hexStr.length === 3) {
    hexStr = hexStr
      .split("")
      .map((char) => char + char)
      .join("");
  }
  if (hexStr.length !== 6) return null;
  const r = parseInt(hexStr.slice(0, 2), 16),
    g = parseInt(hexStr.slice(2, 4), 16),
    b = parseInt(hexStr.slice(4, 6), 16);
  if (isNaN(r) || isNaN(g) || isNaN(b)) return;
  return `rgb(${r}, ${g}, ${b})`;
};

console.log(hex2Rgb("#FF0000")); // rgb(255, 0, 0)
console.log(hex2Rgb("#F00")); // rgb(255, 0, 0)"
console.log(hex2Rgb("0033FF")); // rgb(0, 51, 255)
console.log(hex2Rgb("#123")); // rgb(17, 34, 51)
console.log(hex2Rgb("#12345")); // null
```
