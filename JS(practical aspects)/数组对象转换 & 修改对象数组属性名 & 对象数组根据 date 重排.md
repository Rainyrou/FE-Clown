题一：

```js
const obj = {
  data: [
    ["xiaoming", "male", "18", "beijing", "2020-01-02"],
    ["xiaofang", "female", "18", "shanghai", "2020-03-02"],
  ],
  columns: [
    { name: "name", note: "" },
    { name: "gender", note: "" },
    { name: "age", note: "" },
    { name: "address", note: "" },
    { name: "registerTime", note: "" },
  ],
};

function format(obj) {
  const ans = [],
    keys = obj.columns.map((item) => item.name);
  for (let i = 0; i < obj.data.length; ++i) {
    const perObj = {};
    for (let j = 0; j < keys.length; ++j) perObj[keys[j]] = obj.data[i][j];
    ans.push(perObj);
  }
  return ans;
}

console.log(format(obj));
```

优解：

```js
const format = (obj) => {
  const keys = obj.columns.map((item) => item.name);
  return obj.data.map((item) =>
    item.reduce((acc, val, index) => {
      acc[keys[index]] = val;
      return acc;
    }, {})
  );
};
```

每次迭代后 `reduce` 将回调返回值作为 `acc` 新值，若无 `return acc`，则 `acc` 无法更新，最终外层 `map` 将 `reduce` 的结果收集至一个数组中

输出结果为：

```json
[
  {
    name: 'xiaoming',
    gender: 'male',
    age: '18',
    address: 'beijing',
    registerTime: '2020-01-02'
  },
  {
    name: 'xiaofang',
    gender: 'female',
    age: '18',
    address: 'shanghai',
    registerTime: '2020-03-02'
  }
]
```

题二：

```js
const arrayToObject = (arr) => {
  const obj = {};
  for (const item of arr) {
    const { name, value } = item;
    obj[name] = value;
  }
  return obj;
};

const list = [
  { name: "clown", value: 666 },
  { name: "Alice", value: 30 },
  { name: "Bob", value: 40 },
  { name: "Charlie", value: 50 },
];

console.log(arrayToObject(list)); // { clown: 666, Alice: 30, Bob: 40, Charlie: 50 }
```

修改对象数组属性名：

```js
const data = [
  { name: "苹果", price: "1.0" },
  { name: "香蕉", price: null },
  { name: "", price: "2.0" },
  { name: "橘子", price: undefined },
  { name: "葡萄", price: "" },
];

const convert = (data) => {
  const ans = [];
  for (const item of data) {
    let cur = {};
    const { name, price } = item;
    if (name !== "") {
      cur.label = name;
      cur.value = price === null || price === undefined ? 0 : Number(price);
      ans.push(cur);
    }
  }
  return ans;
};

console.log(convert(data));
```

```js
const data = [
  { name: "苹果", price: "1.0" },
  { name: "香蕉", price: null },
  { name: "", price: "2.0" },
  { name: "橘子", price: undefined },
  { name: "葡萄", price: "" },
];

const convert = (data) =>
  data
    .filter((item) => item.name.trim() !== "")
    .map((item) => ({
      label: item.name,
      value: item.price ? parseFloat(item.price) : 0,
    }));

console.log(convert(data));
```

后端返回的某组数据顺序一一对应，如第一组为 date：2019-12-30 uv: 20，第二组为 date：2021-01-26 uv: 16，根据 date 值以递增排序得到新 data

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
