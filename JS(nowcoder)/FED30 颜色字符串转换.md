将 rgb 颜色字符串转换为十六进制的形式，如 rgb(255, 255, 255) 转为 #ffffff

1. rgb 中每个 , 后面的空格数量不固定
2. 十六进制表达式使用六位小写字母
3. 如果输入不符合 rgb 格式，返回原始输入

示例 1

```
输入：'rgb(255, 255, 255)'
输出：#ffffff
```

![[1693494252680.png]]


**改进版**

![[1693553092828.png]]


```JavaScript
const [_, r, g, b] = match.map(Number);
```

数组的解构赋值

如果 `match` 是 `["rgb(255,255,255)", "255", "255", "255"]`，则结果是 `[NaN, 255, 255, 255]`

```JavaScript
/^rgb\((\d{1,3})\s*,\s*(\d{1,3})\s*,\s*(\d{1,3})\)$/
```

当你使用正则表达式进行字符串匹配时，如果正则表达式中包含圆括号（即捕获组），那么匹配的结果数组不仅会包含整个匹配的字符串，还会包括圆括号中的每个子匹配

当你使用这个正则表达式来匹配字符串 `"rgb(255,255,255)"` 时，返回的 `match` 数组会如下：

- `match[0]`：是整个匹配的字符串，也就是`"rgb(255,255,255)"`
- `match[1]`：是第一个捕获组匹配的内容，也就是`"255"`
- `match[2]`：是第二个捕获组匹配的内容，也就是`"255"`
- `match[3]`：是第三个捕获组匹配的内容，也就是`"255"`

因此，`match` 数组确实会是 `["rgb(255,255,255)", "255", "255", "255"]`

[Array.prototype.join() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/join)

[Object.prototype.toString() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)

##### eval()

[eval() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/eval)

`eval()` 函数可以将传入的字符串当做 JavaScript 代码进行执行

###### 参数

一个字符串，这个字符串可以是一个 JavaScript 表达式、单个语句或者多个语句

###### 返回值

- 如果字符串表示的是一个表达式，`eval()` 会返回该表达式的求值结果
- 如果字符串没有明确的返回值（如仅是一个赋值语句），则返回 `undefined`

###### 注意事项

- 不必使用 `eval()` 来执行算术表达式，因为 JavaScript 本身可以求值。但当你的表达式是由字符串构成的（例如基于某些动态的计算），可以使用 `eval()` 进行求值

```JavaScript
let x = 5;
let strExpression = "3 * x + 2";
let res = eval(strExpression); // 返回17
```

* 如果传递给 `eval()` 的参数不是字符串，`eval()` 会将参数原封不动地返回。例如，使用 `new String()` 构造的字符串对象不会被 `eval()` 执行，除非明确地转换为原始字符串

```JavaScript
eval(new String("2 + 2")); // 返回包含"2 + 2"的字符串对象
eval("2 + 2"); // 返回4
```

为避免此种情况，可以使用 `toString()` 方法：

```JavaScript
let expression = new toString("2 + 2");
eval(expression.toString()); // 返回4
```

- 如果不是直接调用 `eval()`，例如通过引用进行调用，从 ECMAScript 5 开始，这将会在全局作用域中执行，而不是在当前局部作用域。在 `eval()` 中执行的代码可能无法访问调用它的函数中的局部变量

* 使用 `eval()` 可能会带来安全风险。它可以执行任何代码，因此恶意代码也可能被执行。另外过度使用 `eval()` 还可能导致代码难以维护和调试

文档过于抽象，未完待续