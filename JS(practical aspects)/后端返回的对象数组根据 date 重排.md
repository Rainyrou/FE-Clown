后端返回的某组数据顺序一一对应，如第一组为 date：2019-12-30 uv: 20，第二组为 date：2021-01-26 uv: 16，根据 date 值以从小到大顺序重新排序得到新的 data

```json
const data = {
  uv: ["20", "16", "43", "16", "11", "13", "54"],
  date: [
    "2019-12-30",
    "2021-01-26",
    "2020-10-28",
    "2021-01-27",
    "2021-01-29",
    "2021-01-24",
    "2021-01-25",
  ],
};
```

面试时的解法：

```js
const getData = (data) => {
  const date = [],
    map = new Map(),
    uv = [];
  for (let i = 0; i < data.uv.length; ++i) map.set(data.date[i], data.uv[i]);
  for (item of data.date) {
    const newItem = item.split("-").join("");
    date.push(newItem);
  }
  date.sort();
  for (let i = 0; i < date.length; ++i)
    date[i] = `${date[i].slice(0, 4)}-${date[i].slice(4, 6)}-${date[i].slice(
      6,
      8
    )}`;
  for (let i = 0; i < date.length; ++i) {
    if (map.has(date[i])) uv.push(map.get(date[i]));
  }
  return { uv, date };
};
```

优解：

```js
const getData = (data) => {
  const map = data.date.map((date, index) => ({ date, uv: data.uv[index] }));
  map.sort((a, b) => a.date.localeCompare(b.date));
  return {
    date: map.map((item) => item.date),
    uv: map.map((item) => item.uv),
  };
};
```
