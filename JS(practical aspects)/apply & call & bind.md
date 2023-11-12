每个 `Function` 对象都存在 `apply()`、`call()`、`bind()` 方法，其作用都是可以在特定的作用域中调用函数，等于设置函数体内 `this` 对象的值，以扩充函数赖以运行的作用域

##### 区别

1. 执行方式：`call` 和 `apply` 会立即调用函数，而 `bind` 返回一个函数，需要另外单独调用
2. 参数传递：
* `call`：逐个传递
- `apply`：以数组形式传递
- `bind`：逐个传递，返回的函数还可以接收另外的参数

3. 返回值：`call` 和 `apply` 返回函数的执行结果，而 `bind` 返回一个新函数
4. 常见用途：
   - `call` 和 `apply` 常用于借用其他对象的方法
   - `bind` 常用于预先设置某些参数，延迟函数执行，或在事件监听器中绑定上下文

#### Function.prototype.call()

```JavaScript
Function.prototype._mycall = function(thisArg, ...args) {
    thisArg = thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
    // 使用`Symbol`创建一个唯一的属性名
    const fn = Symbol();
    // 将当前函数（即`this`）赋值给`thisArg`对象的`fn`属性
    // 而不用担心覆盖`thisArg`上已有的属性或方法
    // 这样我们可以通过`thisArg[fn]()`来调用它
    thisArg[fn] = this;
    const res = thisArg[fn](...args);
    // 在函数调用完成后，我们不再需要`thisArg`上的`fn`属性
    // 避免内存泄漏，并保持`thisArg`的原始状态
    delete(thisArg[fn]);
    return res;
};
```

测试用例：

```JavaScript
function greet(msg) {
    console.log(`${msg}, ${this.name}`);
};

let person = { name: 'Clown' };

greet._mycall(person, 'Hello');
```

##### 官方文档解读

```JavaScript
call(thisArg[, arg1[, arg2[, ...]]]);
```

`thisArg`: 在调用 `func` 函数使用的 `this` 值，如果函数处于非严格模式下，则指定为 `null` 或 `undefined` 时会自动替换为全局对象，且原始值将被转化为对象

`arg1, arg2, ...`: 可选，指定的参数列表

#### Function.prototype.apply()

```JavaScript
Function.prototype._myapply = function(thisArg, args = []) {
    thisArg = thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
    const fn = Symbol();
    thisArg[fn] = this;
    const res = thisArg[fn](...args);
    delete(thisArg[fn]);
    return res;
};
```

测试用例：

```JavaScript
function greet(frame1, frame2) {
    console.log(`${this.name} likes ${frame1} and ${frame2}`);
}

let person = { name: 'Clown' };

greet._myapply(person, ['Vue', 'React']);
```

###### 为什么传递的参数为 `args = []` ？

为 `args` 参数提供一个默认值，这是 ES6 的参数默认值特性。这里的 `args = []` 并不会直接覆盖传入的参数。当我们调用 `_myapply` 方法并传递了第二个参数时，`args` 会接收这个传递的参数。只有当我们不传递第二个参数时，`args` 才会被赋值为默认的空数组

##### 官方文档解读

```JavaScript
apply(thisArg, [argsArray]);
```

`thisArg`: 在调用 `func` 函数使用的 `this` 值，如果函数处于非严格模式下，则指定为 `null` 或 `undefined` 时会自动替换为全局对象，且原始值将被转化为对象

`argsArray`: 可选，数组或类数组对象，指定调用  `func`  时的参数，如果该值为 `null` 或 `undefined`，则表示无需传入任何参数

#### Function.prototype.bind()

```JavaScript
Function.prototype._mybind = function(thisArg, ...args) {
	// 由于箭头函数不绑定`this`，所以使用`_this`存储当前的`this`引用
	const _this = this;
    thisArg = thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
    return (...newArgs) => {
	    const fn = Symbol();
		thisArg[fn] = _this;
        const res = thisArg[fn](...args, ...newArgs);
        delete(thisArg[fn]);
        return res;
    }
};
```

###### 为什么这里需要传入一些新的参数 `...newArgs` ？

`bind` 的核心功能是允许你设置函数被调用时的 `this` 上下文，并允许你预先提供一些参数。但当绑定后的函数被调用时，仍可以为其传递更多参数

##### 官方文档解读

```JavaScript
bind(thisArg[, arg1[, arg2[, ...]]]);
```

###### 参数

`thisArg`：这是新函数调用时应使用的 `this` 值

- 如果使用 `new` 关键字来调用绑定函数，这个 `thisArg` 会被忽略
- 如果 `thisArg` 是原始值，并且是在 `setTimeout` 等函数中作为回调使用的，那么这些原始值会被转换为相应的对象形式
- 如果没有提供 `thisArg`，或者它是 `null` 或 `undefined`，执行作用域的 `this` 将被用作新函数的 `thisArg`

`arg1, arg2, ...`：当目标函数被调用时，被预置入绑定函数的参数列表中的参数

###### 返回值

返回一个原函数的拷贝，它的 `this` 值是 `bind()` 调用时提供的 `thisArg`，同时它拥有初始参数

###### 注意事项

`bind()` 创建的新函数称为绑定函数（bound function），这个绑定函数包装了原始的函数对象。当你调用这个绑定函数时，它实际上是在调用那个被包装的原始函数

绑定函数具有以下内部属性：

- `[[BoundTargetFunction]]`：被绑定的原始函数
- `[[BoundThis]]`：调用绑定函数时要使用的 `this` 值
- `[[BoundArguments]]`：绑定函数的预置参数列表
- `[[Call]]`：这是一个内部方法，用于执行与该对象关联的代码

当你调用一个绑定函数时，实际上你是在调用它的 `[[BoundTargetFunction]]` 属性，并且使用 `[[BoundThis]]` 作为 `this` 值，`[[BoundArguments]]` 作为参数列表

如果使用 `new` 关键字来调用这个绑定函数，它会表现为目标函数已经被构建完毕了。提供给 `bind()` 的 `this` 值在这种情况下会被忽略，但那些预置的参数仍会被使用
