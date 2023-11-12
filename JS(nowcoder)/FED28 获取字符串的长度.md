如果第二个参数 `bUnicode255For1` === true，则所有字符长度为 1  
否则如果字符 Unicode 编码 > 255 则长度为 2

![[1693487807175.png]]

##### String.prototype.charCodeAt()

[String.prototype.charCodeAt() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt)

- Unicode 是一个字符集，它试图为世界上的每一个字符分配一个唯一的编号。这些编号被称为码位
- UTF-16: UTF-16 是 Unicode 的一种编码方式。在这种编码方式中，大多数常用的字符（例如，拉丁字母、数字、常用标点符号等）使用一个 16 位数字来表示，它们的码位介于 `0` 和 `65535` 之间。然而，Unicode 中的一些字符有超过 65535 的码位。要表示这些字符，UTF-16 使用了一种称为"代理对"的技术，其中每个字符由两个 16 位数字组成

* `charCodeAt()` 方法返回字符串中给定索引位置的字符的 UTF-16 码元值。这个值是一个介于 0 和 65535 之间的整数

```JavaScript
charCodeAt(index);
```

###### 参数

它接受一个索引作为参数，表示要检索其码元的字符的位置。如果没有提供参数或提供 `undefined`，则默认值为 0

###### 返回值

如果索引超出字符串的长度，返回 `NaN`

###### 注意事项

- 由于 Unicode 的某些字符在 UTF-16 中由两个 16 位数字（代理对）表示，所以 `charCodeAt()` 只能返回这两个数字中的一个。对于这些字符，`charCodeAt()` 返回的不是完整的 Unicode 码位

例如，对于 Unicode 码位为 `0x1D306`（代数符号 𝌆）的字符，它在 UTF-16 中由两个 16 位数字 `0xD834` 和 `0xDF06` 表示。如果你调用`charCodeAt()` 来获取这个字符的码元，你只会得到第一个数字。为了获得完整的码位，你需要调用 `charCodeAt()` 两次：一次获得第一个数字，一次获得第二个数字

- 为解决上述问题，JavaScript 提供了 `codePointAt()` 方法，它返回字符串中给定索引位置的字符的完整 Unicode 码位。对于代理对中的字符，你不需要调用两次 `charCodeAt()`，而只需要调用一次 `codePointAt()`
