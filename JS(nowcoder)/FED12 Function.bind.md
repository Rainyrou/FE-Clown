请补全 JavaScript 代码，要求实现 Function.bind 函数的功能且该新函数命名为"\_bind"

![[1693273328294.png]]

- `thisArg`：指定新函数调用时的 `this` 值。`otherArgs`：预置到新函数的参数列表
- 在 `thisArg` 对象上创建一个临时方法 `fn`，并将其指向当前函数（使用 `this` 引用）

```JavaScript
return (...newArgs) => {
	const allArgs = [...otherArgs, ...newArgs];
	return thisArg.fn(...allArgs);
}
```

注意，由于我们使用了箭头函数，这个新函数内部的 `this` 值与外部函数（即 `_bind` 方法）的 `this` 值相同。这确保了我们可以安全地访问`thisArg.fn`，即那个临时存储的原始函数

##### 为什么这里需要传入一些新的参数 `...newArgs` ？

`bind` 的核心功能是允许你设置函数被调用时的 `this` 上下文，并允许你预先提供一些参数。但当绑定后的函数被调用时，仍可以为其传递更多参数

##### Function.prototype.bind()

[Function.prototype.bind() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

当调用 `bind()` 方法时，它会返回一个新函数。这个新的函数在调用时会使用 `bind()` 方法中提供的 `thisArg` 作为其 `this` 值。同时，任何在 `bind()` 中提供的附加参数都会被预置到新函数的参数列表中

```JavaScript
function.bind(thisArg[, arg1[, arg2[, ...]]]);
```


