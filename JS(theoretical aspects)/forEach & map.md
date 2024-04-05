###### forEach

- `forEach` 方法对数组的每个元素执行一次提供的函数，但它不会返回任何值即返回 `undefined`
- `forEach` 允许修改原始数组的元素

```JavaScript
const array = [1, 2, 3, 4, 5];

// 打印数组的每个元素
array.forEach(item => console.log(item));

// forEach 不返回任何内容
const result = array.forEach(item => item * 2);
console.log(result); // undefined
```

###### map

- `map` 方法对数组的每个元素执行一次提供的函数，然后返回一个新数组，这个数组包含应用该函数后的结果
- `map` 不修改调用它的原始数组

```JavaScript
const array = [1, 2, 3, 4, 5];

// 创建一个新数组，每个元素都是原数组对应元素的两倍
const doubled = array.map(item => item * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

// 原始数组保持不变
console.log(array); // [1, 2, 3, 4, 5]
```

