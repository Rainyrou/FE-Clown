1. `Array.isArray`

`unknown` 明确入参类型未知，强制研发通过显式判断类型后操作值

```ts
function isArray<T = unknown>(value: unknown): value is T[] {
  return Object.prototype.toString.call(value) === "[object Array]";
}
```

2. `forEach`

```js
const _forEach = (arr, callback) => {
  for (let i = 0; i < arr.length; i++) callback(arr[i], i, arr);
};
```

3. `map`

```js
const _map = (arr, callback) => {
  const ans = [];
  for (let i = 0; i < arr.length; i++) ans.push(callback(arr[i], i, arr));
  return ans;
};
```

4. `filter`

```js
const _filter = (arr, callback) => {
  const ans = [];
  for (let i = 0; i < arr.length; i++) {
    if (callback(arr[i], i, arr)) ans.push(arr[i]);
  }
  return ans;
};

const nums = [1, 2, 3, 4, 5, 6];
const evens = _filter(nums, (num) => num % 2 === 0);
console.log(evens); // [ 2, 4, 6 ]
```

回调函数的三个参数分别为：

* `element`：当前正被处理的元素
* `index`：当前元素的索引
* `array`：当前正被处理的原始数组

5. `reduce`

```js
const _reduce = (arr, callback, initialVal) => {
  if (arr.length === 0 && initialVal === undefined)
    throw new TypeError("Reduce of empty array with no initial value");
  let acc = initialVal !== undefined ? initialVal : arr[0];
  const startIndex = initialVal !== undefined ? 0 : 1;
  for (let i = startIndex; i < arr.length; ++i)
    acc = callback(acc, arr[i], i, arr);
  return acc;
};

const nums = [1, 2, 3, 4, 5, 6];
const sum = _reduce(nums, (a, b) => a + b, 0);
console.log(sum); // 21
```

6. `flat` 

递归：

```JavaScript
const _flat = (arr) => {
  const ans = [];
  const flatten = (nums) => {
    for (const item of nums) {
      if (Array.isArray(item)) flatten(item);
      else ans.push(item);
    }
  };
  flatten(arr);
  return ans;
};

console.log(_flat([1, 2, 3, [4, [5]], 1, 2, 6, 7]));
```

迭代：

```js
const _flat = (arr) => {
  const stk = [...arr],
    ans = [];
  while (stk.length) {
    const next = stk.pop();
    if (Array.isArray(next)) stk.push(...next);
    else ans.unshift(next);
  }
  return ans;
};

console.log(_flat([1, 2, 3, [4, [5]], 1, 2, 6, 7]));
```

7. `push & unshift & pop`

```js
const _push = (arr, ...nums) => [...arr, ...nums];
const _unshift = (arr, ...nums) => [...nums, ...arr];
const _pop = (arr) => {
  if (arr.length === 0) return undefined;
  const last = arr[arr.length - 1];
  arr.length -= 1;
  return last;
};
```

8. `append`

```js
const append = (nums, ...args) => {
  const arr = Array.isArray(nums) ? [...nums] : [];
  const filtered = args.filter((item) => item !== undefined && item !== null);
  return [...arr, ...filtered];
};
```