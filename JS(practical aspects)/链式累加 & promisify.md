###### 链式累加

变式一：

```js
function add(...args) {
  const sum = args.reduce((a, b) => a + b, 0);
  return function (...newArgs) {
    return newArgs.length ? add(sum, ...newArgs) : sum;
  };
}

console.log(add(1, 2, 3)(4, 5)(6)()); // 21
```

变式二：

```js
function sum(...args) {
  const allArgs = [...args];
  const fn = (...newArgs) => {
    allArgs.push(...newArgs);
    return fn; // 链式收集
  };
  fn.sumoff = () => allArgs.reduce((a, b) => a + b, 0);
  return fn;
}

console.log(sum(1, 2).sumoff()); // 3
console.log(sum(1, 2)(3).sumoff()); // 6
console.log(sum(1)(2)(3)(4).sumoff()); // 10
```

变式三：

```js
const add = (a, b) =>
  new Promise((resolve) => setTimeout(() => resolve(a + b), 1000));

const calculate = async (...nums) => {
  let sum = 0;
  for (let i = 0; i < nums.length; ++i) sum = await add(sum, nums[i]);
  return sum;
};

calculate(1, 2, 3, 4)
  .then((res) => console.log(res))
  .catch((err) => console.error(err));
```

###### promisify

根据题意：改写 sum 函数

```js
const asyncAdd = (a, b, cb) =>
  setTimeout(() => cb(null, a + b), Math.floor(Math.random() * 1000));

asyncAdd(3, 5, (err, result) => console.log(result)); // 8

async function sum(...args) {
  if (args.length < 2) return args[0] || 0;
  if (args.length === 2) return await asyncAdd(args[0], args[1]);
  const len = args.length;
  const half = len >> 1;
  const left = await sum(...args.slice(0, half + 1));
  const right = await sum(...args.slice(half + 1));
  res = asyncAdd(left, right);
  return res;
}

(async () => {
  const result1 = await sum(1, 4, 6, 9, 2, 4);
  const result2 = await sum(3, 4, 9, 2, 5, 3, 2, 1, 7);
  const result3 = await sum(1, 6, 0, 5);
  console.log([result1, result2, result3]); // [26, 36, 12]
})();
```

`await` 的作用为"等待一个 Promise 完成并返回其结果"，但 `asyncAdd` 内部调用 `setTimeout`，而 `setTimeout` 的返回值为定时器 ID，新增的 `promisifyAsyncAdd` 函数补全 `cb` 参数 + 将回调转换为 Promise

```js
const promisifyAsyncAdd = (a, b) =>
  new Promise((resolve, reject) =>
    asyncAdd(a, b, (err, result) => (err ? reject(err) : resolve(result)))
  );
  
async function sum(...args) {
  if (args.length < 2) return args[0] || 0;
  if (args.length === 2) return await promisifyAsyncAdd(args[0], args[1]);
  const len = args.length;
  const half = len >> 1;
  const left = await sum(...args.slice(0, half + 1));
  const right = await sum(...args.slice(half + 1));
  return await promisifyAsyncAdd(left, right);
}
```

`promisify` 将基于回调的函数转换为返回 Promise 的函数

```js
function promisify(fn) {
  return function (...args) {
    return new Promise((resolve, reject) =>
      fn(...args, (err, result) => (err ? reject(err) : resolve(result)))
    );
  };
}

const fs = require("fs");
const readFileAsync = promisify(fs.readFile);
readFileAsync("./index.html", "utf8")
  .then((data) => console.log(data))
  .catch((err) => console.error(err));
```

