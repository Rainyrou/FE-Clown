```JavaScript
// 参数依次为：定期执行的函数 `fn`、`fn` 函数执行之间的延迟、传递给 `fn` 的参数列表
function _mysetInterval(fn, delay = 4, ...args) {
    let cancel = false;
    const task = () => {
        setTimeout(() => {
            if(!cancel) {
	            // `this` 指向 `_mysetInterval`
	            // 一旦`_mysetInterval`被调用，`task`函数会立即执行
				// 重新设置一个新的 `setTimeout`，实现定期执行的效果
                fn.apply(this, args);
                task();
            }
        }, delay);
    }
    task();
    // `_mysetInterval`返回一个函数，当这个函数被调用时，它会设置`cancel`变量为`true`
    // 从而在下一次`setTimeout`回调时阻止`fn`函数的进一步执行
    return () => { cancel = true };
};
```

* 递归：`task` 函数递归地调用自身来模拟 `setInterval` 的效果
* 闭包：由于 `cancel` 和 `task` 都是在 `_mysetInterval` 函数内部定义的，它们在函数外部是不可见的，但在函数内部可以相互访问

###### 什么时候 `cancel` 会被设置为 `true` ？

`_mysetInterval` 返回的匿名函数是一个取消功能，允许你停止定期调用 `fn`。举个例子(测试用例)：

```JavaScript
// 使用_mysetInterval定义一个每隔1秒打印"Hello"的函数
// `stopHello`保存了`_mysetInterval`返回的匿名函数
const stopHello = _mysetInterval(() => {
    console.log('Hello');
}, 1000);

// 5秒后停止定期打印
setTimeout(() => {
    stopHello(); // 此时调用了_mysetInterval返回的那个匿名函数
}, 5000);
```

可以看到控制台上神奇的一幕：

![[1697509242812.png]]

