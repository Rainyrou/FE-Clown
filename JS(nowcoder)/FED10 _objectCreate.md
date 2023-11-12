请补全 JavaScript 代码，要求实现 Object.create 函数的功能且该新函数命名为"\_objectCreate"

![[1693219019202.png]]

- 检查传入的 `proto` 是否是一个对象或者 `null`。如果不是，函数会提前返回这段代码和 MDN 中描述的不完全相同，根据 MDN，如果 `proto` 不是对象或 `null`，应该抛出一个 `TypeError`

##### 为什么要通过一个临时的构造函数，创建了一个新的对象？而不能直接创建了一个新的对象？

在 JavaScript 中，每个对象都有一个与之关联的原型。当你试图访问一个对象的属性，但该对象自己并没有这个属性时，JavaScript 会查找该对象的原型以看看那是否有这个属性。这个查找的过程会沿着原型链一直进行，直到找到该属性或者达到了原型链的顶端（通常是`Object.prototype`）

为了设置一个对象的原型为指定的其他对象，你需要在创建这个对象的时候就确定它的原型。在 ES5 之前的 JavaScript 版本中，主要的方式是通过构造函数和它的 `prototype` 属性来达到这个目的

现在，考虑以下情况：你想要创建一个新对象，并设置它的原型为一个已存在的对象（称为 `proto`）。如果直接创建一个新对象，例如通过 `{}` 或 `new Object()`，这个新对象的原型会被默认设置为 `Object.prototype`。如果你想改变这个新对象的原型为 `proto`，直接操作是不可能的，因为直接创建的对象的原型已经是 `Object.prototype` 了

但是，构造函数提供了一个间接的方法来达到这个目的。你可以创建一个临时的构造函数，设置它的 `prototype` 属性为 `proto`，然后实例化这个临时的构造函数，这样得到的新实例会有它的原型设置为 `proto`

在 ES6 及后续版本中，`Object.setPrototypeOf()` 方法被引入，允许直接设置一个已存在对象的原型。但在 ES5 及之前的版本中，使用构造函数是设置新对象原型的主要方法

##### Object.create()

[Object.create() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

**`Object.create()`**  静态方法以一个现有对象作为原型，创建一个新对象

所谓原型，就是对象可以继承其属性和方法的对象。在 JavaScript 中，当你尝试访问一个对象的属性，而该对象没有该属性时，JavaScript 会查找该对象的原型链，以查看其中是否存在这个属性

```JavaScript
Object.create(proto);
Object.create(proto, propertiesObject);
```

###### 参数

- `proto`：新创建对象的原型对象

- `propertiesObject` (可选)：这个参数允许你为新创建的对象定义额外的属性，这些属性的描述符与 `Object.defineProperties()` 方法所接受的相同。你可以使用这个参数来详细定义属性，如它们是否可写、是否可枚举等

###### 返回值

- 这个方法返回一个新的对象，该对象的原型是 `proto` 参数。如果指定了 `propertiesObject`，那么新对象还会包含这些属性

###### 异常

- 如果 `proto` 参数不是对象也不是 `null`，方法会抛出一个 `TypeError` 异常。这是因为原型链的终点必须是 `null`，所以只有对象和 `null` 是合法的原型
