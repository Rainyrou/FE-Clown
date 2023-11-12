封装函数 f，使 f 的 this 指向指定的对象

##### 解法一：`.bind`

[Function.prototype.bind() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

```JavaScript
function bindThis(f, oTarget) {
	return f.bind(oTarget);
}
```

- 当你调用一个函数的 `.bind()` 方法并传递一个对象作为参数时，它会返回一个新的函数。这个新函数当被调用时，会有与原始函数相同的行为，但其 `this` 值会被绑定到你传递给 `.bind()` 的对象。这就是为什么你不需要显式地传递 `arguments`

- `.bind()` 还可以部分绑定参数。除第一个参数（用于 `this` 绑定）外，你传递给 `.bind()` 的任何其他参数都会成为返回的函数预先提供的参数。但在此例中，我们并没有利用这一特性。此外，`bind` 不是立即调用的，返回的是一个函数，用例在外面调用时传入了参数，也就不用传 `arguments`

- `.bind()` 方法本身返回的是一个新函数，你要知道的是，`bindThis` 函数的目标是返回一个新的函数，这个新函数在调用时会以特定的 `this`（`oTarget`）来调用原始函数 `f`。这也就是为什么下面的 `.apply` 和 `.call` 解法 return 了两次的原因

##### 解法二：`.bind`

[Function.prototype.bind() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

```JavaScript
function bindThis(f, oTarget) {
	return function() {
		return f.apply(oTarget, arguments);
	}
}
```

- 当我们使用 `.apply()` 方法调用一个函数时，必须为该函数提供一个参数数组，因为 `.apply()` 允许你调用一个函数并为其提供一个 `this` 值和一个参数数组

* 如果没有这个外部的 `return`，那么 `bindThis` 就会直接执行函数 `f` 并返回它的结果，而不是返回一个函数

* 内部的 `return` 会返回原始函数 `f` 的结果。当我们最后调用通过 `bindThis` 生成的函数时，这确保了我们不仅以指定的 `this` 值调用 `f`，而且还会返回 `f` 的结果

###### 能不能不带 `arguments` ？

- 如果你知道要传递给 `f` 函数的确切参数，那么可以像这样使用 `.apply()`：

```JavaScript
return f.apply(oTarget, [arg1, arg2]);
```

但这种方式是硬编码的，不具有普适性

- 如果你不知道要传递给 `f` 的参数或参数数量是动态的，使用 `arguments` 对象是合适的，因为它能够捕获所有传递给函数的参数

##### 解法三：`.call`

[Function.prototype.call() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

`.call()` 和 `.apply()` 的区别是：`.call()`  方法接受的是一个参数列表，而  `.apply()`  方法接受的是一个包含多个参数的数组

```JavaScript
function bindThis(f, oTarget) {
	return function() {
		return f.call(oTarget, ...arguments);
	}
}
```


