n -> [2, 16]

十进制转 n 进制：

```js
const convert = (num, base) => {
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
  const digits = "0123456789abcdef";
  let ans = 0,
    power = 1;
  for (let i = str.length - 1; i >= 0; --i) {
    const val = digits.indexOf(str[i]);
    ans += val * power;
    power *= base;
  }
  return ans;
};

console.log(convert("1010", 2)); // 10
console.log(convert("ff", 16)); // 255
console.log(convert("12", 8)); // 10
```
