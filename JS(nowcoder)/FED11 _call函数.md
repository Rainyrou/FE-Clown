请补全 JavaScript 代码，要求实现 Function.call 函数的功能且该新函数命名为"\_call"

![[1693219998910.png]]

实现目标：`_call` 函数会临时修改内部 this 的指向并返回结果

- `[...arguments]` 将类数组对象 `arguments` 转化为一个真正的数组。这里的 `arguments` 包含所有传递给 `_call` 的参数，包括 `target`

- `.shift()` 方法从数组中移除第一个元素（即 `target`）并返回它。因此，现在数组仅包含原始 `call` 方法的参数。这是为了模拟 `Function.prototype.call` 的行为。当你使用 `call` 方法时，第一个参数是你想要调用的函数的 `this` 上下文，随后的所有参数都是传递给这个函数的。为了实现这一点，我们需要从 `arguments` 中移除 `target` 参数，然后将剩余的参数传递给函数

##### 关于 `target`

###### 为什么 `target` 参数是可选的？

在 JavaScript 中，`this` 是在函数调用时确定的，而不是在函数声明或定义时确定的。为了模拟 `Function.prototype.call()` 的行为，`target` 参数（代表 `this`）被设定为可选，因为在 JavaScript 中，你可以不传递任何上下文而直接调用一个函数。例如：

```JavaScript
const demo = () => {
	console.log(this);
}

demo.call();
```

###### 为什么没有传入时，它的默认值为 `window` ？

- 在浏览器环境中，如果一个函数是在全局作用域调用的，且未通过任何对象调用，则其默认的 `this` 上下文是全局对象，即 `window`（在浏览器中）。然而，这个行为在严格模式（strict mode）下是不同的；在严格模式下，`this` 的值为 `undefined`，除非明确设置

- 在全局作用域中，变量和函数默认都是 `window` 对象的属性和方法

- 这种设计是为了确保在没有明确设置上下文时，函数仍然有一个默认的执行上下文。它确保了 JavaScript 在浏览器环境中的一致性和预测性。但是，这种默认行为在严格模式下会被改变，主因为将 `this` 默认设置为全局对象可能会导致意外的副作用，尤其是当试图修改 `this` 时

##### 为什么要为 `target` 添加一个临时方法 `fn` ?

- `target['fn']` 实际上是一个临时属性，用于将当前函数（即你想要调用的函数）存储到目标对象中，这里的 `this` 关键字表示调用 `_call` 方法的函数。通过给 `target` 对象分配一个临时方法名为 `fn`，我们可以确保这个方法在 `target` 上下文中运行

- `call` 方法的核心功能是允许我们在指定的 `this` 上下文（由 `target` 给出）中执行一个函数。为了实现这一点，我们需要将该函数暂时视为 `target` 的方法，因为在 JavaScript 中，当你调用一个对象的方法时，这个方法内部的 `this` 关键字会自动指向那个对象。所以，`target['fn'] = this` 这步操作是为了"借用"该函数并在特定的 `this` 上下文中运行它

在 JavaScript 中，对象的属性可以通过两种主要方式来访问和设置：点符号（`.`）和括号符号（`[]`）

```JavaScript
const obj = {};
obj['name'] = "clown";
console.log(obj['name']); // "clown"
```

括号符号在某些情况下更具灵活性，例如当属性名是动态的、包含空格或其他特殊字符时，或者是一个变量时

##### 为什么要执行 `delete target['fn']` ?

- 为了清理我们之前所做的修改，以确保 `target` 对象保持不变。考虑一个情景：如果 `target` 对象已经有了一个名为 `fn` 的方法或属性，然后我们暂用这个名称，那么在调用完成后，我们应当恢复 `target` 的原始状态。即使原始的 `target` 对象没有 `fn `属性，最佳实践仍然是清理我们所做的临时修改，以避免可能的副作用或冲突

##### 关于返回值

- 返回的 `ans` 值与 `this` 无关，它是函数执行的结果。当你使用 `call`（或 `_call`）方法时，你不仅可以指定函数的 `this` 上下文，还可以传递参数给这个函数，并获得其返回值。例如，如果你有一个函数 `greet`，这个函数会返回一个问候消息，并且这个消息基于 `this.name`，那么通过 `_call` 你可以在不同的 `this` 上下文中运行它，并获取不同的问候消息

```JavaScript
const greet = () => {
	return `Hello, ${this.name}!`;
}

const person1 = { name: 'Clown' };
const person2 = { name: 'GG Bond' };

console.log(greet._call(person1)); // "Hello, Clown!"
console.log(greet._call(person2)); // "Hello, GG Bond!"
```

##### 还有一件事

```JavaScript
	target['fn'] = this;
	const result = target['fn']([...arguments].shift());
	delete target['fn'];
```

从技术上讲，我们可以简化代码如下：

```JavaScript
const result = this.apply(target, [...argument].slice(1));
```

这里我们使用 `apply` 代替了直接调用，因为 `apply` 允许我们明确设置 `this` 值并传递参数数组。但是，这种方式不会模拟 JavaScript 内部的行为，尽管它仍然可以达到相同的效果

#### Function.prototype.call()

`call()`  方法允许你在特定的上下文中调用一个函数，你可以指定函数内部的 `this` 值，其后的参数是你想传递给函数的参数列表

```JavaScript
function.call(thisArg, arg1, arg2);
```

###### 参数

- `thisArg` (可选)：这是你想在调用的函数内部作为 `this` 值使用的对象。如果在严格模式下且没有提供 `thisArg`（或者传入 `null` 或`undefined`），那么 `this` 值会是 `undefined`。在非严格模式下，如果你传入 `null` 或 `undefined`，它会自动替换为全局对象（在浏览器中通常是 `window`）。如果你传入一个原始值（如数字或字符串），这个原始值会被自动转换为它的对象形式（如 `new Number()` 或 `new String()`）
- `arg1, arg2, ...` ：指定的参数列表

###### 返回值

- 返回调用的函数的返回值。如果函数没有返回值，`call()` 会返回 `undefined`

###### 注意事项

- `call()` 为不同的对象分配和调用属于一个对象的函数或方法
- `call()` 可以实现继承。假设你有一个构造函数，并且你不想在每个子类中重新定义构造函数的方法。你可以在子类的构造函数中使用 `call()` 来调用父构造函数，确保 `this` 是子类的实例

说的太抽象了，文档解析未完待续
