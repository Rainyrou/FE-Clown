`lodash.get`：

```js
const _get = (obj, path, val) => {
  const keys = Array.isArray(path)
    ? path
    : path
        .replace(/\[(\d+)\]/g, (all, match) => `.${match}`)
        .split(".")
        .filter(Boolean);
  for (const key of keys) {
    if (!obj) return val;
    obj = obj[key];
  }
  return obj || val;
};
```

`lodash.set`：修改根对象并返回根对象而非嵌套层级的子对象

```js
const _set = (obj, path, val) => {
  const keys = Array.isArray(path)
    ? path
    : path
        .replace(/\[(\d+)\]/g, (all, match) => `.${match}`)
        .split(".")
        .filter(Boolean);
  let cur = obj;
  for (let i = 0; i < keys.length - 1; ++i) {
    const key = keys[i];
    if (!cur[key]) cur[key] = isNaN(Number(keys[i + 1])) ? {} : [];
    cur = cur[key];
  }
  cur[keys[keys.length - 1]] = val;
  return obj;
};
```

对象扁平化：

`Object.assign` 将一至多个源对象的可枚举属性浅拷贝至目标对象并返回目标对象

```js
const flattenObj = (target, prefix = "") => {
  const obj = {};
  for (const key in target) {
    if (target.hasOwnProperty(key)) {
      const newKey = prefix
        ? Array.isArray(target)
          ? `${prefix}[${key}]`
          : `${prefix}.${key}`
        : key;
      if (typeof target[key] === "object" && target[key] !== null)
        Object.assign(obj, flattenObj(target[key], newKey));
      else obj[newKey] = target[key];
    }
  }
  return obj;
};

const obj1 = {
  a: { a1: "vaa", b1: "bb" },
  b: ["b1", { ac: 2 }],
  c: "c1",
};

const obj2 = {
  a: "hello",
  b: 111,
  c: {
    d: "world",
    e: [1, 2, 3],
  },
};

console.log(flattenObj(obj1));
console.log(flattenObj(obj2));
/*{ 'a.a1': 'vaa', 'a.b1': 'bb', 'b[0]': 'b1', 'b[1].ac': 2, c: 'c1' }
{
  a: 'hello',
  b: 111,
  'c.d': 'world',
  'c.e[0]': 1,
  'c.e[1]': 2,
  'c.e[2]': 3
}*/
```

对象转换路径：

```js
const obj = {
  user: {
    docs: {
      "Letter.txt": {},
    },
    photos: {
      2024: {
        "japan.png": {},
      },
      "trip.jpg": {},
    },
  },
  var: {
    log: {
      "system.log": {},
    },
  },
};

const dfs = (obj, prefix = "", result = []) => {
  for (const key in obj) {
    const cur = `${prefix}/${key}`,
      value = obj[key];
    if (Object.keys(value).length === 0) result.push(cur);
    else dfs(value, cur, result);
  }
  return result;
};

console.log(JSON.stringify(dfs(obj), null, 2));
/*[
  "/user/docs/Letter.txt",
  "/user/photos/2024/japan.png",
  "/user/photos/trip.jpg",
  "/var/log/system.log"
]*/

const dfs = (obj, prefix = "", result = []) => {
  for (const key in obj) {
    const cur = prefix ? `${prefix}.${key}` : key,
      value = obj[key];
    if (Object.keys(value).length === 0) result.push(cur);
    else dfs(value, cur, result);
  }
  return result;
};

console.log(JSON.stringify(dfs(obj), null, 2));
/*[
  "user.docs.Letter.txt",
  "user.photos.2024.japan.png",
  "user.photos.trip.jpg",
  "var.log.system.log"
]*/
```

路径转换对象：

```js
const convert = (paths) => {
  const obj = {};
  for (const path of paths) {
    const parts = path.split("/").filter(Boolean);
    let cur = obj; // 重置当前层级指针至根对象
    for (let i = 0; i < parts.length; ++i) {
      const isLast = i === parts.length - 1;
      if (isLast) {
        cur[parts[i]] = {};
      } else {
        if (!cur[parts[i]]) cur[parts[i]] = {};
        cur = cur[parts[i]]; // 指针移至下一层级
      }
    }
  }
  return obj;
};

const paths = [
  "/user/docs/Letter.txt",
  "/user/photos/trip.jpg",
  "/user/photos/2024/japan.png",
  "/var/log/system.log",
];

console.log(JSON.stringify(convert(paths), null, 2));
```

嵌套对象的路径取值：

```js
const getValueByStr = (obj, str) => {
  const keys = str.split(".");
  let cur = obj;
  for (const key of keys) {
    if (cur && typeof cur === "object" && key in cur) cur = cur[key];
    else return undefined;
  }
  return cur;
};

const data = {
  user: {
    name: "John Doe",
    address: {
      city: "New York",
      zip: "10001",
    },
    contact: {
      email: "john.doe@example.com",
      phone: "123-456-7890",
    },
  },
};

console.log(getValueByStr(data, "user.name")); // John Doe
console.log(getValueByStr(data, "user.address.city")); // New York
console.log(getValueByStr(data, "user.contact.email")); // john.doe@example.com
console.log(getValueByStr(data, "user.age")); // undefined
console.log(getValueByStr(data, "user.address.country")); // undefined
```