请补全 JavaScript 代码，要求实现对象参数的深拷贝并返回拷贝之后的新对象

注意：
1. 参数对象和参数对象的每个数据项的数据类型范围仅在数组、普通对象（{}）、基本数据类型中
2. 无需考虑循环引用问题

![[1693303911851.png]]


深拷贝：复制一个对象及其嵌套对象，以便源对象和复制的对象之间完全独立，修改一个对象不会影响另一个对象

```JavaScript
cloneTarget[prop] = _sampleDeepClone(target[prop]);
```

* 它递归地调用自身来克隆目标对象的属性。确保深度复制，嵌套对象也会被复制，而不仅仅是外层对象

确保所复制的对象及其嵌套对象完全独立，即修改克隆后的对象不会影响原始对象，反之亦然。深拷贝与浅拷贝相对，浅拷贝只复制对象的外层，如果对象内有其他对象或数组，浅拷贝只复制引用，而不是真正的复制内部对象或数组

###### 为什么要这样做？

举一个简单的例子：你有一个对象，它的一个属性是另一个对象

```JavaScript
const person = {
    name: "John",
    address: {
        city: "New York",
        zip: "10001"
    }
}
```

假设你想克隆这个 `person` 对象。如果你简单地将 `person.address` 赋值给 `clonePerson.address`，你只是复制了一个引用，而不是整个嵌套的地址对象。结果是，当你修改 `clonePerson.address.city` 时，`person.address.city` 也会被修改，因为它们都指向同一个对象

为了避免此种情况，你需要为 `person.address` 创建一个完整的新副本。这就是 `_sampleDeepClone(target[prop])` 所做的。它递归地对每个属性进行深拷贝，确保完全**独立**于原始对象

###### 底层原理

基于递归。函数对每个对象属性进行递归，直到它遇到基本数据类型（基本类型是通过值而不是引用传递的），然后开始返回并逐步构建深拷贝的结果

在JavaScript中，对象和数组是通过引用传递的，而基本数据类型是通过值传递的。当你将一个对象赋值给另一个变量时，你实际上只是复制了一个指向该对象的引用，而不是对象本身。为了复制整个对象及其所有嵌套属性，你需要递归地复制每个属性

###### 注意事项

1. 这个简化的深拷贝函数不考虑函数、`RegExp`、`Date`、`Map`、`Set` 等特殊对象。它们在克隆时可能不会按预期工作
2. 它不考虑循环引用。如果对象中有属性引用了对象自身或其任何祖先，则会导致无限递归，最终抛出一个最大调用堆栈大小超出的错误
3. 它不处理对象的原型链。克隆的对象总是从 `Object.prototype` 继承的



