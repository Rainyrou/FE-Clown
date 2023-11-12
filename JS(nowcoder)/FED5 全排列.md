请补全 JavaScript 代码，要求以数组的形式返回字符串参数的所有排列组合。

注意：

1. 字符串参数中的字符无重复且仅包含小写字母
2. 返回的排列组合数组不区分顺序

示例 1：

```
输入：_permute('abc')

输出：['abc','acb','bac','bca','cab','cba']
```

![[1693101540747.png]]

##### String.prototype.split()

[String.prototype.split() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/split)

**`split()`**  方法接受一个模式，通过搜索模式将字符串分割成一个有序的子串列表，将这些子串放入一个数组，并返回该数组

```JavaScript
split(separator);
split(separator, limit);
```

###### 参数

1. `separator`：指定了哪些位置或模式应被当作分隔符

- 如果没有提供 `separator` 或者传递了 `undefined`，`split()` 方法会返回一个数组，该数组只包含原始字符串
- 字符串: 比如 `,`，会按照这个字符串为分隔符来分割
- 具有 `Symbol.split` 方法的对象，典型的例子是正则表达式
- 如果传入的值不是 `undefined` 并且对象不具有 `@@split` 方法，那么这个值将被强制转换为字符串，然后用作分隔符

  2.`limit` (可选)：限制结果数组中子字符串的数量。

- 如果提供了 `limit`，`split` 方法会按照指定的 `separator` 分割字符串，但在达到 `limit` 数量的子字符串后，它将停止分割
- 如果字符串在达到指定的 `limit` 数量之前就已经被完全分割了，返回的数组的长度可能会小于 `limit`
- 如果 `limit` 是 `0`，返回一个空数组 `[]`

###### 返回值

在给定字符串中出现  `separator`  的每一个点上进行分割而成的字符串数组

###### 注意事项

- 如果传入一个非空字符串作为 `separator`，那么原始字符串会在每次出现这个 `separator` 的地方被分割。分割结果中不包括 `separator`本身

```JavaScript
const myString = "Hello\0offer\Clown";
const result = myString.split("\t");
console.log(result); // ["Hello", "0offer", "Clown"];
```

- 如果 `separator` 包含多个字符，只有当这整个字符序列都出现时才会分割
- 如果 `separator` 出现在字符串的开始或结束，返回的数组中的第一个或最后一个元素将是一个空字符串
- 如果 `separator` 在整个字符串中都没有出现，那么返回的数组只会包含一个元素，即整个字符串

* 如果 `separator` 是一个空字符串，那么原始字符串会被转换为一个数组，这个数组的每一个元素都是原始字符串的一个字符

```JavaScript
const str = "Hello";
const result = str.split("");
console.log(result); // ["H", "e", "l", "l", "o"];
```

- 当使用匹配空字符串的正则表达式作为 `separator` 时，如何分割字符串取决于正则表达式是否设置了 `u` 标志
- 如果没有设置 `u` 标志，字符串会按 UTF-16 码元（code unit）进行分割。对于那些需要两个码元来表示的字符（例如，某些表情符号），这会导致字符被拆分
- 如果设置了 `u` 标志，字符串会按 Unicode 码位（code point）进行分割，确保每个字符都是完整的

```JavaScript
"😄😄".split(/(?:)/);   // [ "\ud83d", "\ude04", "\ud83d", "\ude04" ]
"😄😄".split(/(?:)/u); // [ "😄", "😄" ]
```

- 在正则表达式中，捕获括号由普通的圆括号 `()` 来定义。如果 `separator` 是一个包含捕获括号的正则表达式，每当 `separator` 匹配到一个位置，捕获括号内匹配到的内容都会被添加到结果数组中，这是由正则表达式对象的 `Symbol.split` 方法指定的

- 如果传递给 `split()` 方法的 `separator` 是一个对象，并且这个对象有 `Symbol.split` 方法，那么这个方法会被调用。在调用 `Symbol.split` 方法时，会将原始字符串和 `limit` 作为参数，而 `this` 关键字会设置为 `separator` 对象。`Symbol.split` 方法的返回值会直接作为 `split()` 方法的返回值

##### Array.prototype.join()

[Array.prototype.join() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/join)

**`join()`**  方法将一个数组（或一个类数组对象）的所有元素连接成一个字符串并返回该字符串，用逗号或指定的分隔符字符串分隔。如果数组只有一个元素，那么将返回该元素而不使用分隔符

```JavaScript
join();
join(separator);
```

###### 参数

`separator`（可选）：用于在数组的每个元素之间作为分隔符。如果省略，默认使用逗号 `,` 作为分隔符。如果 `separator` 是一个空字符串（`""`），那么连接的字符串中不会有任何分隔符

###### 返回值

返回连接后的字符串。如果数组为空，则返回空字符串

###### 注意事项

- 如果元素的值是 `undefined` 或 `null`，那么它们会被视为空字符串，而不是字面上的"undefined"或"null"字符串
- 当你在数组上调用 `toString()` 方法时，实际上在内部，它会调用 `join()` 方法且不带任何参数。如果你在特定的数组实例上重写了 `join` 方法，那么它的 `toString` 行为也会被相应地改变
- 在稀疏数组上调用 `join()` 时，这些未定义的索引（或"空槽"）的值会被视为为 `undefined`，因此在连接的字符串中会被当作空字符串

```JavaScript
console.log([1,,3].join()); // '1,,3'
console.log([1,undefined,3].join()); // '1,,3'
```

- `join()` 方法是通用的，只要对象具有 `length` 属性和整数键（类似于数组的索引），那么 `join()` 方法也可以在这些对象上使用。这种特性是由于 JavaScript 中的动态类型和原型继承的灵活性

```JavaScript
const arrayLike = {
	length: 3,
	0: 2,
	1: 3,
	2: 4
};
console.log(Array.prototype.join.call(arrayLike)); // 2,3,4
```

##### Array.prototype.forEach()

[Array.prototype.forEach() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)

**`forEach()`**  方法对数组的每个元素执行一次给定的函数

```JavaScript
forEach(callbackFn);
forEach(callbackFn, thisArg);
```

###### 参数

`callbackFn`：为数组中每个元素执行的函数。并会丢弃它的返回值。这个函数可以接受三个参数：当前元素（element）、当前元素的索引（index）和调用了 `forEach()` 的数组本身（array）

`thisArg`（可选）：当执行 `callbackFn` 时指定的 `this` 的值。如果不传递 `thisArg`，`callbackFn` 内部的 `this` 将是 `undefined`

###### 返回值

`undefined`

###### 注意事项

- `forEach()` 按索引的升序为数组中的每个元素调用 `callbackFn`
- 不同于 `map()`（可以返回一个新数组），`forEach()` 总是返回 `undefined`，因此不能用于链式调用

- `forEach()` 不会为稀疏数组中的空槽调用 `callbackFn`

```JavaScript
const arr = [1, 3, , 7];
let count = 0;

arr.forEach((element) => {
	console.log({ element });
	++count;
});

console.log({ count });
// { element: 1 }
// { element: 3 }
// { element: 7 }
// { count: 3 }
```

- `forEach()` 自身不会更改调用它的数组，但在 `callbackFn` 中可以更改数组
- 当 `forEach()` 开始执行时，数组的长度已被保存。如果在迭代过程中更改了数组的长度，`callbackFn` 不会为超出初始长度的新元素调用
- 如果更改了已经访问过的索引的值，`callbackFn` 不会为它们再次调用
- 如果在 `callbackFn` 中更改了一个已经存在但尚未访问的元素的值，则当访问到该元素时，`callbackFn` 将使用这个新值。已经被删除的元素在迭代过程中不会被访问

```JavaScript
const arr = [1, 2, 3, 4, 5];
arr.forEach((element, index, array) => {
	console.log(`Element: ${element}, Index: ${index}, Array: ${array}`);
});
```

- `forEach()` 方法是通用的，你可以在非数组对象上调用它，只要该对象具有 `length` 属性和整数键

```JavaScript
let obj = {
	0: 'a',
	1: 'b',
	2: 'c',
	length: 3
};

Array.prototype.forEach.call(obj, item => {
	console.log(item); // a b c
})
```

- `forEach()` 本身不提供停止迭代或中断循环的机制。即使在回调函数中使用 `return` 也只是跳过当前迭代，并不停止循环。如果需要有条件地中断循环，应该考虑使用其他循环方法，例如 `for`、`for...of` 或 `for...in`，因为在这些方法中可以使用 `break` 语句来提前终止循环
- `forEach()` 是同步的，如果在 `forEach()` 的回调中使用 Promise 或异步函数，那么 `forEach()` 会立即继续到下一个迭代，而不会等待异步操作完成。因此，使用异步操作时，需要注意可能的问题，并考虑使用其他方法来处理异步逻辑，例如 `for...of` 循环配合 `async/await`

```JavaScript
const nums = [1, 2, 3, 4];
let sum = 0;

const getSum = async (a, b) => a + b;

nums.forEach(async (nums) => {
	sum = await getSum(sum, nums);
});

console.log(sum); // 0
```
