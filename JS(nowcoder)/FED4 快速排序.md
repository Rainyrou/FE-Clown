请补全 JavaScript 代码，要求将数组参数中的数字从小到大进行排序并返回该数组。

注意：

1. 数组元素仅包含数字
2. 请优先使用快速排序方法

示例 1:

```
输入：_quickSort([0,-1,1,-2,2])

输出：[-2,-1,0,1,2]
```

![[1693008552532.png]]

[Array.prototype.splice() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

[Array.prototype.concat() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)

##### Array.prototype.splice()

`splice()` 方法用于更改数组，可以增删、替换数组中的元素

```JavaScript
splice(start)
splice(start, deleteCount)
splice(start, deleteCount, item1)
splice(start, deleteCount, item1, ..., itemN)
```

###### 参数：

`start`

- 它是数组中开始更改的位置，它的基数是 0
- 如果 `start < 0` ，从数组的末尾开始计算，即 `start + array.length`
- 如果 `start < -array.length`，它会被当作 `0`
- 如果 `start >= -array.length`，则不会删除任何元素，但会添加任何传递进来的新元素
- 如果 `start` 被省略，则不会删除任何元素；而 `start` 如果为 `undefined`，它会被当作 `0`

`deleteCount` (可选)

- 从  `start`  开始数组要删除的元素数量
- 如果省略此值或其值大于等于由  `start`  指定的位置到数组末尾的元素数量，从  `start`  到数组末尾的所有元素都会被删除
- 如果值为 `0` 或负数，则不会删除任何元素

`item1, …, itemN` (可选)

- 从  `start`  开始要添加到数组中的元素
- 如果不指定任何元素，`splice()` 只会从数组中删除元素

###### 返回值

它返回一个数组，该数组包含从原始数组中删除的元素。如果只删除了一个元素，它返回一个仅含该元素的数组。如果没有删除任何元素，返回空数组

###### 注意事项

- 当你使用 `splice()` 时，它将更改调用它的数组本身，而不是返回一个新数组。这和其他一些数组方法如 `slice()` 不同，后者不更改原始数组，而是返回一个新数组
- `splice()` 可能会更改  `this`  的内容
- 稀疏数组是其元素不是连续的数组。例如，`let arr = []; arr[10] = 'hello';` 创建了一个稀疏数组，其中前 10 个位置是空的。如果你在这个数组上使用 `splice()` 方法并删除一部分，结果数组可能也会是稀疏的
- `splice()` 可以在任何伪数组对象上使用，只要该对象具有 `length` 属性和整数键属性
- JavaScript 的字符串是不可变的，一旦创建了字符串，你就不能更改它。尽管字符串具有很多与数组相似的方法和属性


##### Array.prototype.concat()

**`concat()`** 方法用于合并两个或多个数组

```JavaScript
concat()
concat(value0)
concat(value0, value1)
concat(value0, value1, ..., valueN)
```

* `concat` 方法可以接受任意数量的数组或值作为参数，并将它们合并到一个新数组中
* 如果你不传递任何参数给 `concat` 方法，它仅返回一个现存数组的拷贝

```JavaScript
let arr = [1, 2, 3];
let newArr = arr.concat();
console.log(newArr); // [1, 2, 3]
```

* `concat` 方法不会修改现有的数组，而是创建并返回一个新数组
* 如果你给 `concat` 传递一个数字或对象，这个数字或对象本身会成为新数组的一个元素。但如果你传递一个数组，该数组的内容会被合并到新数组中，而不是数组本身

```JavaScript
let arr = [1, 2, 3];
let newArr = arr.concat(4, [5, 6], {a: 7});
console.log(newArr); // [1, 2, 3, 4, 5, 6, {a: 7}];
```




