n 小于等于 16

十进制转 n 进制：

```js
const convert = (num, base) => {
  if (base < 2 || base > 16) throw new Error("Base must be between 2 and 16.");
  if (num === 0) return "0";
  const digits = "0123456789abcdef";
  let ans = "",
    cur = num;
  while (cur > 0) {
    const remain = cur % base;
    ans = digits[remain] + ans;
    cur = Math.floor(cur / base);
  }
  return ans;
};

console.log(convert(10, 2)); // 1010
console.log(convert(255, 16)); // ff
console.log(convert(10, 8)); // 12
```

n 进制转十进制：

```js
const convert = (str, base) => {
  if (base < 2 || base > 16) throw new Error("Base must be between 2 and 16.");
  const digits = "0123456789abcdef",
    lowerStr = str.toLowerCase();
  let ans = 0,
    power = 1;
  for (let i = lowerStr.length - 1; i >= 0; i--) {
    const val = digits.indexOf(lowerStr[i]);
    if (val === -1 || val >= base)
      throw new Error(`Invalid character for base ${base}.`);
    ans += val * power;
    power *= base;
  }
  return ans;
};

console.log(convert("1010", 2)); // 10
console.log(convert("ff", 16)); // 255
console.log(convert("12", 8)); // 10
```
