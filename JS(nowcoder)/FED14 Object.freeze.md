请补全 JavaScript 代码，要求实现 Object.freeze 函数的功能且该新函数命名为"\_objectFreeze"

![[1693284421990.png]]

##### Object.freeze()

[Object.freeze() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)

`Object.freeze()`  静态方法可以使一个对象被冻结。冻结对象可以防止拓展，并使现有的属性不可写入和不可配置。被冻结的对象不能再被更改：不能添加新的属性，不能移除现有的属性，不能更改它们的可枚举性、可配置性、可写性或值，对象的原型也不能被重新指定。`freeze()`  返回与传入的对象相同的对象

```JavaScript
Object.freeze(obj);
```

###### 参数

`obj`: 指定冻结的对象

###### 返回值

返回传递给这个函数的对象

###### 注意事项

- 当尝试修改、增删被冻结对象的属性时，操作会失败。在非严格模式下，这种失败是静默的，而在严格模式下，会抛出一个 `TypeError` 异常
- 虽然不能更改被冻结对象的数据属性的值，但若对象有访问器属性（getters 和 setters），setter 仍可以被调用，只是不能更改其实现或删除它
- 当对象的属性值也是一个对象时，这个子对象并不会因为父对象被冻结而自动被冻结
- 如果类型数组和 `DataView` 有元素，则不能冻结它们，因为它们是对内存的视图，冻结可能会导致问题。但若没有元素，它们可以被冻结

##### Object.getOwnPropertyNames()

[Object.getOwnPropertyNames() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames)

- `Object.getOwnPropertyNames` 静态方法返回一个数组，其包含给定对象中所有自有属性（包括可枚举和不可枚举属性，但不包括使用 symbol 值作为名称的属性）
- 静态方法即直接在 `Object` 上调用，而不是在实例上
- 自有属性即直接存在于该对象上，而不是从其原型链上继承来的

```JavaScript
Object.getOwnPropertyNames(obj);
```

###### 参数

`obj`: 你想要获取其属性名称的对象。该对象的自有属性名称都会被返回

###### 返回值

返回一个字符串数组，其中每个字符串都是对象的一个自有属性的名称

###### 注意事项

- 数组中的属性名是根据特定的规则排序的。对象的非负整数键（无论是否可枚举）会按照升序被添加到数组中。其次按照它们被插入到对象中的顺序，其他的字符串键会被添加到数组中
- 在 ES5 之前的版本中，当你将一个非对象作为 `Object.getOwnPropertyNames()` 的参数，JavaScript 会抛出一个 `TypeError`。但从 ES6 开始，如果你传递一个非对象参数给这个方法，它会先被转换为对象，再尝试获取它的属性名

##### Object.defineProperty()

[Object.defineProperty() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

`Object.defineProperty()`  静态方法会直接在一个对象上定义一个新属性，或修改其现有属性，并返回此对象

```JavaScript
Object.defineProperty(obj, prop, descriptor);
```

###### 参数

- `obj`：要定义属性的对象。

- `prop`：一个字符串或  `Symbol`，指定要定义或修改的属性键

- `descriptor`：要定义或修改的属性的描述符

###### 返回值

- 传入函数的对象，其指定的属性已被添加或修改

###### 描述符的属性

1. `configurable`

当你为对象的属性设置 `configurable` 为 `false`：

- 你不能改变属性的类型，即不能将数据描述符转换为访问器描述符，反之亦然
- 该属性不能从对象中删除
- 该属性的描述符中的其他设置（如 `enumerable`、`writable` 等）都不能被修改。但有个例外，如果当前属性是一个数据描述符且设置了`writable` 为 `true`，你还是可以修改它的 `value`，并且可以将 `writable` 更改为 `false`
- 默认情况下，`configurable` 的值为 `false`

2. `enumerable`

- `enumerable` 设置为 `true` 时，这个属性会在对象的属性枚举中出现，如在 `for...in` 循环或 `Object.keys()` 方法中
- 默认情况下，`enumerable` 的值为 `false`

3. `value`: 属性的实际值。默认值为 `undefined`

4. `writable`: 如果设置为 `true`，属性的值可以被重新赋值。默认值为 `false`

5. `get`: 当属性被访问时会被调用。该函数没有参数，并将返回一个值，这个返回值就是属性的值

6. `set`: 当属性被赋予一个新值时会被调用。该函数接收一个参数，即要设置的新值

###### 描述符类型

- 如果描述符只有 `value` 或 `writable` 其中之一或两者都有，但没有 `get` 和 `set`，它就被视为数据描述符
- 如果描述符有 `get` 或 `set`，但没有 `value` 和 `writable`，它就被视为访问器描述符
- 如果描述符同时有数据描述符和访问器描述符的键，那么 `Object.defineProperty()` 会抛出异常

###### 注意事项

- 直接赋值创建的属性可以被更改、删除，且可枚举。但使用 `Object.defineProperty()` 默认创建的属性则不然
- 如果一个属性已经定义了 setter，`Object.defineProperty()` 修改该属性时不会调用这个 setter
- 描述符可以从它的原型链上继承属性，为了确保默认值得到保留，可以采用以下方法：

1. 预先冻结描述符对象原型链中的对象
2. 明确指定所有选项
3. 使用 `Object.create(null)` 创建一个没有原型的对象

##### Object.preventExtensions()

[Object.preventExtensions() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions)

- `Object.preventExtensions()`  静态方法可以防止新属性被添加到对象中（即防止该对象被扩展）。它还可以防止对象的原型被重新指定

```JavaScript
let obj = { a: 1 };
Object.preventExtensions(obj);
obj.b = 2;
console.log(obj.b); // undefined
```

- 在非严格模式下，向不可扩展的对象添加新属性会静默失败（没有错误消息或警告），代码只是简单地不执行该操作。但在严格模式下，尝试这样做会抛出一个 `TypeError`

- 虽然我们不能向不可扩展的对象添加新属性，但仍可以向该对象的原型添加属性。这意味着原型链上的属性仍可以被该对象继承

```JavaScript
let obj = {};
Object.preventExtensions(obj);
Object.prototype.newProp = "I'm a new prototype property!"
console.log(obj.newProp); // "I'm a new prototype property!"
```

- 一旦我们使用 `Object.preventExtensions()` 方法，对象的内部 `[[Prototype]]` 属性将不可修改。尝试更改它将抛出 `TypeError`

- 一旦一个对象被标记为不可扩展，你不能再更改它的状态使其可扩展。这个操作是不可撤销的
