```JavaScript
const findRepeat = (str, num) => {
  const seen = new Set(),
    ans = new Set();
  for (let i = 0; i <= str.length - num; ++i) {
    const sub = str.slice(i, i + num);
    if (seen.has(sub)) ans.add(sub);
    else seen.add(sub);
  }
  return [...ans];
};

const string = "ababbaba";
const number = 2;
console.log(findRepeat(string, number));
```