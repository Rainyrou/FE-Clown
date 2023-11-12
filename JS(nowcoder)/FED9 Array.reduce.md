请补全 JavaScript 代码，要求实现 Array.reduce 函数的功能且该新函数命名为"\_reduce"

示例 1

```
输入：[1,2,3]._reduce((left, right) => left + right)

输出：6
```

![[1693197426881.png]]

如果初始值 `pre` 没有定义，第一次累加的时候，处理了 `this[i]` 和 `this[i + 1]`，下一次加，要从第三个元素开始，所以 `i` 被递增两次
